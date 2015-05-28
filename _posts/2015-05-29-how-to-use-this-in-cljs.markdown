---
title: How to use `this` in CLJS
---

Occasionally, you have to break out of your normal cljs/FP flow and access whatever object [`this`][msdn-this] refers to. In most cases, you'll need to do so because:

1. You're interacting with a JS lib
2. You're using jQuery (e.g. `($ this)`)

cljs provides the [`this-as`][this-as] macro to access `this`. When [initially writing a line-reader][line-reader], I used `this-as` to access/set data on the transformer:

{% highlight clojure %}
;;interacting with a JS lib
(defn- transform [chunk encoding done]
  (this-as this ;;it's idiomatic to name it `this`
    (let [data (if (.-_lastLineData this)
                 (str (.-_lastLineData this) chunk)
                 (str chunk))
          lines (clojure.string/split data (js/RegExp. eol "g"))]
      (set! (.-_lastLineData this) (last lines))
      (doseq [line (butlast lines)]
        (.push this line))
      (done))))

(defn- flush [done]
  (this-as this
    (if (.-_lastLineData this)
      (.push this (.-_lastLineData this)))
    (set! (.-_lastLineData this) nil)
    (done)))
{% endhighlight %}

This is a departure from normal cljs in that we're mutating the object referred to by `this`.

With React libraries like Om, Reagent, and Quiescent, I've used jQuery less and less. It's rare that I need to access `this`, but cljs makes it easy to do so when necessary.

[this-as]: https://github.com/cljsinfo/api-refs/blob/catalog/refs/cljs.core_this-as.md
[msdn-this]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this
[line-reader]: http://clojurescriptmadeeasy.com/blog/cljs-read-files-line-by-line-on-nodejs.html
