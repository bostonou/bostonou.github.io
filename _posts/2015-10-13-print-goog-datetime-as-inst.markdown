---
title: How to print goog.DateTime as &#35;inst
---

For a recent problem, I was working with `goog.DateTime` objects and needed to send them along in JSON, specifically as `#inst`.

The [docs for `goog.DateTime`][goog-docs] say they can be used interchangeably with native js `Date` objects, but a quick check shows that cljs doesn't automatically do what I want:

{% highlight clojure %}
cljs.user=> (pr-str (js/Date. "October 13, 2015"))
"#inst \"2015-10-13T05:00:00.000-00:00\"" ;;what we want
cljs.user=> (pr-str (DateTime. (js/Date. "October 13, 2015")))
"#object[Object 20151013T000000]" ;;what we get :(
{% endhighlight %}

There are several ways to do this (e.g. convert the `DateTimes` to native `Date` objects), but really I just want `DateTime` objects to print like `Date` objects without having to think about it.

Here's the implementation for printing native js `Date`:

{% highlight clojure %}
(defn- pr-writer-impl
  ;;details elided...

  (cond
    ;;more details...

    ;; Use the new, more efficient, IPrintWithWriter interface when possible.
    (implements? IPrintWithWriter obj)
    (-pr-writer ^not-native obj writer opts)

    ;;more details...

    (instance? js/Date obj)
    (let [normalize (fn [n len]
                      (loop [ns (str n)]
                        (if (< (count ns) len)
                          (recur (str "0" ns))
                          ns)))]
      (write-all writer
        "#inst \""
        (str (.getUTCFullYear obj))             "-"
        (normalize (inc (.getUTCMonth obj)) 2)  "-"
        (normalize (.getUTCDate obj) 2)         "T"
        (normalize (.getUTCHours obj) 2)        ":"
        (normalize (.getUTCMinutes obj) 2)      ":"
        (normalize (.getUTCSeconds obj) 2)      "."
        (normalize (.getUTCMilliseconds obj) 3) "-"
        "00:00\""))
    ;;details elided...
  ))
{% endhighlight %}

So we see the `js/Date` print implementation, which we'd like to use for `DateTime`. How to do that? Well, we see the check for `implements? IPrintWithWriter`, so if we implement that protocol, we can print however we want. (It'd be nice if that code was separated into it's own function, but it's not a big deal to just copy it.) We want to apply this for every `DateTime` object, so we can extend the `DateTime` object via [`extend-type`][extend-type]:

{% highlight clojure %}
(ns cljs.made-easy
  (:import [goog.date DateTime]))

(extend-type DateTime
  IPrintWithWriter
  (-pr-writer [obj writer _opts]
    (let [normalize (fn [n len]
                      (loop [ns (str n)]
                        (if (< (count ns) len)
                          (recur (str "0" ns))
                          ns)))]
      (write-all writer
                 "#inst \""
                 (str (.getUTCFullYear obj))             "-"
                 (normalize (inc (.getUTCMonth obj)) 2)  "-"
                 (normalize (.getUTCDate obj) 2)         "T"
                 (normalize (.getUTCHours obj) 2)        ":"
                 (normalize (.getUTCMinutes obj) 2)      ":"
                 (normalize (.getUTCSeconds obj) 2)      "."
                 (normalize (.getUTCMilliseconds obj) 3) "-"
                 "00:00\""))))
{% endhighlight %}

Now, we get what we want:

{% highlight clojure %}
cljs.user=> (pr-str (DateTime. (js/Date. "October 13, 2015")))
"#inst \"2015-10-13T05:00:00.000-00:00\""

cljs.user=> (clj->js {:date (js/Date. "October 13, 2015")})
#js {:date #inst "2015-10-13T05:00:00.000-00:00"}

cljs.user=> (clj->js {:date (DateTime. (js/Date. "October 13, 2015"))})
#js {:date #inst "2015-10-13T05:00:00.000-00:00"} ;;aw yeah
{% endhighlight %}

### Take-aways

This was pretty nice to handle and a good exploration of cljs. Via `extend-type` and `IPrintWithWriter`, we can simply control how various objects are printed. Even more generally, we can use `extend-type` and any protocol to add functionality to types/objects, without needing to break them open.

[goog-docs]: https://google.github.io/closure-library/api/class_goog_date_DateTime.html
[extend-type]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/extend-type
