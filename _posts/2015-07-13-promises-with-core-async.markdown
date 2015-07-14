---
title: Promises with core.async
---

> Is there a `core.async` equivalent to `Promise.all`?

One of the things I love about ClojureScript is that it feels like it provides the right building blocks. Only in rare cases does the language hinder me when I'm trying to solve a problem.

When I saw the above question, I knew there had to be either a built-in fn or a simple way to implement it. First, what does `Promise.all` provide?

1. Resolves promises concurrently
2. Returns the values from the list of promises, in the order they were given
3. Returns the value of a rejected promise immediately (i.e. does not wait for other promise resolutions)

*For clarity in mentally mapping core.async to Promises, functions are named as if we were using Promises.*

### Concurrent resolution

With `core.async` `go` blocks, we can run code concurrently:

{% highlight clojure %}
(ns cljs-made-easy.promise
  (:refer-clojure :exclude [into])
  (:require-macros [cljs.core.async.macros :refer [go go-loop]])
  (:require [cljs.core.async :refer [<! >!] :as a]))

(defn resolving-promise [key t]
  ;;go blocks return core.async channels; the last value of the
  ;; go block is the value put to the channel
  (go
    (pr (str key " starting"))))
    (<! (a/timeout t))
    (pr (str key " finished"))))
    ;;our last value is our resolved value
    (str key " waited " t "ms")))

(let [promise-1 (resolving-promise :promise-1 2000)
      promise-2 (resolving-promise :promise-2 1000)]
  (go
    (pr (<! promise-1))
    (pr (<! promise-2))))

;;Output =>
;; ":promise-1 starting"
;; ":promise-2 starting"
;; ":promise-2 finished"
;; ":promise-1 finished"
;; ":promise-1 waited 2000ms"
;; ":promise-2 waited 1000ms"

{% endhighlight %}

### Returning values in order

`core.async` doesn't provide a built-in fn that both takes the value from each channel and returns those values in the order the channels were given. But writing a fn is simple enough:

{% highlight clojure %}
(defn into [coll & chans]
  (go-loop [coll coll
            chans chans]
    (if (seq chans)
      (recur (conj coll (<! (first chans)))
             (rest chans))
      coll)))
{% endhighlight %}

In this fn, we just read from the first channel and store the value into our collection. Then, if there are more channels to read, we recur with the rest of the channels. Again, we return a channel with the collection as a value.

### Rejecting Immediately

To reject immediately, we need to combine two concepts. First, we provide a channel to each "promise" that allows the promise to reject. For the promise to reject, it simply puts its value to the reject channel and cleans up as necessary.

{% highlight clojure %}
(defn resolving-promise [key t reject]
  (go
    (pr (str key " starting"))
    (<! (a/timeout t))
    (str key " waited " t "ms")))

(defn rejecting-promise [key t reject]
  (go
    (pr (str key " starting"))
    (<! (a/timeout t))
    (>! reject (str key " had an error after " t "ms"))
    "This string won't be used"))
{% endhighlight %}

Now, we need to get either the list of resolved values or the rejected value, whichever comes first. The fn `alts!` lets us get the first available value from a list of channels:

{% highlight clojure %}
(defn all [reject & chans]
  (go
      (let [all-chans (apply into [] chans)
            [v ch] (a/alts! [reject all-chans])]
        (if (= ch reject)
          (pr (str "Rejected value: " v))
          (pr (str "Resolved values: " v)))))
{% endhighlight %}

We take our reject channel and list of channels (i.e. promises) and combine our channels using our `into` fn. Then, we wait on either the reject value or the resolved values via `alts!`. If the first value to come back is via the reject channel, we go into our rejection code. Otherwise, we have our ordered list of resolved values and handle them appropriately.

This was a fun little problem that reminded me of why I love cljs. With cljs, we don't have to worry about which browsers implement `Promise.all`. We have the right building blocks to do it ourselves. And `core.async` is seriously awesome; just about every time I use it, it amazes me that this functionality is provided as a library!

*[view gist of the full code][gist]*

[gist]: https://gist.github.com/bostonou/685d946a1e92935461c7
