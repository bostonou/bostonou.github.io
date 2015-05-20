---
title: CLJS - Read files line by line on NodeJS
---
I'm working to port some clj to cljs and needed a cljs version of [`line-seq`][line-seq]. Having spent most of my cljs time in the browser, using NodeJS is a bit of a new world. There's almost certainly a package to read files line by line, but I decided to roll my own because:

* It'd be good experience reading through NodeJS docs
* I'd have to wrap some cljs anyway
* I'm a programmer :)

I saw this [post on streams][post-on-streams] and decided to convert it to cljs.

First of, we load in our NodeJS libs:

{% highlight clojure %}
(def fs (js/require "fs"))
(def stream (js/require "stream"))
(def ^:const eol (.-EOL (js/require "os"))) ;;eg "\n" or "\r\n"
{% endhighlight %}

As we read the file stream, we want to transform it from generic chunks of data to lines of text. We define a transform fn that we'll use on our stream:

{% highlight clojure %}
(defn- transform [chunk encoding done]
  (this-as this
    (let [data (if (.-_lastLineData this)
                 (str (.-_lastLineData this) chunk)
                 (str chunk))
          lines (clojure.string/split data (js/RegExp. eol "g"))]
      (set! (.-_lastLineData this) (last lines))
      (doseq [line (butlast lines)]
        (.push this line))
      (done))))
{% endhighlight %}

We also want to make sure we flush the last bit of data we read:

{% highlight clojure %}
(defn- flush [done]
  (this-as this
    (if (.-_lastLineData this)
      (.push this (.-_lastLineData this)))
    (set! (.-_lastLineData this) nil)
    (done)))
{% endhighlight %}

Lastly, we set up our file stream and pipe it through our transformer:

{% highlight clojure %}
(defn read-file-cb [file-name cb]
  (let [line-reader (.Transform stream #js {:objectMode true})
        source (.createReadStream fs file-name)]
    (set! (.-_transform line-reader) transform)
    (set! (.-_flush line-reader) flush)
    (.pipe source line-reader)
    (.on line-reader "readable"
         (fn []
           (when-let [line (.read line-reader)]
             (cb (str line)) ;;callback with each line
             (recur))))
    nil))
{% endhighlight %}

I generally prefer using channels instead of passing around callbacks, so I actually used this version with [core.async][core.async]:

{% highlight clojure %}
(defn read-file-chan [file-name out-chan]
  (let [line-reader (.Transform stream #js {:objectMode true})
        source (.createReadStream fs file-name)]
    (set! (.-_transform line-reader) transform)
    (set! (.-_flush line-reader) flush)
    (.pipe source line-reader)
    (.on line-reader "readable"
         (fn []
           (go
             (loop []
               (when-let [line (.read line-reader)]
                 (>! out-chan (str line)) ;;push line to chan
                 (recur))))))
    nil))
{% endhighlight %}

This worked, but I really want the laziness of `line-seq` without the asynchronous code. In my next post, I'll implement `line-seq` to work just the way we want.

Here's a [gist of the full code][code].

[line-seq]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/line-seq
[post-on-streams]: https://strongloop.com/strongblog/practical-examples-of-the-new-node-js-streams-api/
[core.async]: http://clojure.github.io/core.async/
[code]: https://gist.github.com/bostonou/77ec1bfe8e4dbdd34fa3
