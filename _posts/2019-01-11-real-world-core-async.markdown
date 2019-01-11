---
title: Real World core.async
tags: cljs-value design how-to
---

How do you display a 10,000 page pdf in the browser without just locking up the UI? Well, you essentially use a lot of smoke and mirrors to only do work that's required, when it's required. There are many different ways to achieve it, but [core.async][core-async] worked really well for our case.

## PDFjs

We used the [impressive PDFjs library][pdfjs] for displaying the pdfs. At a high level, PDFjs fetches the pdf and provides a pdf object. Each page is individually loaded from the pdf object; loading a page takes a nontrivial amount of processing time.

## The Problem

The part of our core.async usage I want to highlight is how we managed loading the pdf. Our requirements:

#### 1. Don't lock up the UI

We can't just load up every page all at once because otherwise the user wouldn't be able to interact with the browser until all of the potentially long loading is complete. So we have to break up the load process so that other events can get processing time.

#### 2. Show the user something as fast as possible

In our context, the users would be working through thousands or millions of documents. Users might be able to make a decision based on the first page or two and move to the next document without ever needing to see the rest of the document. So in addition to the normal good experience of fast loading, there were significant financial implications to our load times.

#### 3. Let the user jump to any page

Because a user might want to jump to any arbitrary page, we couldn't just assume that we could linearly load pages. Again, speed was vital, so if a user jumps to page 100, we needed to load it immediately without waiting for pages 1-99. And from a UX perspective, the user would assume that the document was already loaded when we showed them the first few pages (as requirement #2 states).

## Our Design

### Loading Chan (version 1)

The first decision was to load pages in batches. Each page is loaded by page number, so we create groups of page numbers and push each group onto a channel. That channel is returned and used by someone else that we don't have to care about right now.

{% highlight clojure %}
(defn fill-load-chan [number-of-pages]
  (let [partitions (partition-all NUM-PAGES-PER-LOAD
                                  (range 1 (inc number-of-pages)))
        c (a/chan)]
    (go
      (doseq [page-numbers partitions]
        (a/>! c page-numbers))
      (a/close! c))
    c))
{% endhighlight %}

### Load From The Chan

To load our pages, we simply have a function that reads each batch from the channel. It loads each page in the current batch and then waits for some time before loading the next. You can think of `(a/<! (a/timeout WAIT-BETWEEN-LOAD))` as a `yield`; it "parks" our code and lets other events get processed (requirement #1). In this way, we limit how many pages are being loaded at any one time.

We take a bit of a shortcut in assuming that our wait is long enough for all of the current pages to get loaded. If the wait time isn't long enough, we could end up with multiple batches being loaded at the same time. We could add more coordination to solve that, but in practice it wasn't necessary.

{% highlight clojure %}
(defn load-from-chan [c]
  (go
    (loop []
      (when-let [page-numbers (a/<! c)]
        (doseq [page-number page-numbers]
          (load-page! page-number))
        (a/<! (a/timeout WAIT-BETWEEN-LOAD))
        (recur)))))
{% endhighlight %}

### Final Code (version 1)

{% highlight clojure %}
(def c (fill-load-chan num-pages))
(load-from-chan c))
{% endhighlight %}

This looks good. We have code that batches up the pages to load which isn't tightly coupled with our code that loads each batch of pages. We start with the first pages to meet requirement #2, and we yield between page loads to meet requirement #1. Now we just need to handle requirement #3.

### Priority Chan

If we look at our code, each function is doing its job well and shouldn't need to know about specific page loads. What would be nice is if we could simply put a specific batch of pages at the front of the channel that we load from. In essence, we want to jump a page number to the front of the channel and say, "Process this next, then keep going." (We can assume our code is smart enough to handle attempts to load an already loaded page.)

core.async provides [`mix`][mix] which "creates and returns a mix of one or more input channels which will be put on the supplied out channel". What that means is we can combine channels who's values are put onto a single output channel. That can be a bit of a mind bender; how does it apply in our situation?

If we create a channel that is just for loading specific pages, we can combine (aka mix) the channel with the original channel that has all the batches, and then we load from the mixed channel.

{% highlight clojure %}
(defn load-specific-page [c-mix page-number]
  (let [c (a/chan)]
    (a/admix c-mix c) ;;we now mix in our chan
    (a/put! c [page-number])))

(def main-chan (a/chan))
(def c-mix (a/mix main-chan))

(a/admix c-mix (fill-load-chan num-pages)) ;;we now mix in our chan
(load-from-chan main-chan))

;;load a specific page when needed
(load-specific-page c-mix page-number)
{% endhighlight %}

Neither `fill-load-chan` or `load-from-chan` have changed at all. `fill-load-chan` now has its channel mixed in instead of passing it directly to `load-from-chan`. `load-from-chan`is still just reading data from a channel. `load-specific-page` can now provide data as well that will be processed.

Unfortunately, we haven't solved our problem. Our priority channel we added doesn't actually get priority. `mix` actually allows you to alter how channels are read by setting modes: `:solo`, `:mute`, and `:pause`. For our purposes, we can use `:solo` to give our priority channel the priority it needs.

{% highlight clojure %}
;;final version
(defn load-specific-page [c-mix page-number]
  (let [c (a/chan)]
    (a/toggle queue-mix {c {:solo true}})
    (a/put! c [page-number])
    (a/close! c))
{% endhighlight %}

Instead of simply using [`admix`][admix], we use [`toggle`][toggle] and add our priority channel with a mode of `:solo`. When a channel is added with the `:solo` mode, only channels with that mode have their inputs mixed into the output channel. So what we've essentially done is "paused" our other channels; in other words, our `:solo` channel now has priority over our non-solo channel.

We toggle in our priority channel, put the page we want to load, and then close the channel. That value will be processed next by whatever is processing the output channel. We close our channel because that removes it from the mix, allowing the other channel to become "unpaused". (That doesn't seem to be documented, so I don't know how I know that. Probably read it in the source.)

To recap, we mix two channels into an output channel: one channel that just has batches of page numbers, and one channel that will get a single page that we want to have priority. Our priority channel gets priority via the `:solo` mode. The output channel takes the batches of page numbers by default; when the priority queue gets mixed in, it takes from that channel first.

---
<br />
Hopefully this provides a good example of the flexibility and simplification that core.async provides. We used core.async heavily to manage the asynchronous operations of PDFjs and to avoid the callback hell that can come with a library that uses Promises liberally.

*[You can see the full example code here.][code]*

[core-async]: https://github.com/clojure/core.async
[pdfjs]: https://mozilla.github.io/pdf.js/
[mix]: http://clojure.github.io/core.async/#clojure.core.async/mix
[admix]: http://clojure.github.io/core.async/#clojure.core.async/admix
[toggle]: http://clojure.github.io/core.async/#clojure.core.async/toggle
[code]: https://gist.github.com/bostonou/1fd59e620984e54e3d98fb150e425081
