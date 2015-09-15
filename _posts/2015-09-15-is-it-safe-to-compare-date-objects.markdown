---
title: Is it safe to compare Date objects?
---

In JavaScript, comparing Date objects provides numerous opportunities for bugs:

{% highlight javascript %}
var a = b = new Date("September 15, 2015"),
    c = new Date("September 15, 2015");

a == b; //true
a === b; //true
a == c; //false
a === c; //false
a > c; //false
a < c; //false
a >= c; //true
a <= c; //true
{% endhighlight %}

Does ClojureScript have the same pitfalls? **In cljs, Date equality checks are by value. However, other checks (`<`/`>`) fall through to JavaScript.** Simple checks are fine, but if you're doing much work with dates, you're likely better off using a library like [cljs-time][cljs-time].

### The Details

*The following isn't necessary to know, but it does provide some interesting details, depending on your definition of "interesting."*

So how does ClojureScript handle Date comparison?

The ClojureScript equality check we care about is:

{% highlight clojure %}
(defn ^boolean =
  ;;other arities omitted...
  ([x y]
    (if (nil? x)
      (nil? y)
      (or (identical? x y)
        ^boolean (-equiv x y))))
{% endhighlight %}

Date objects implement the `IEquiv` protocol, which is where we can find the implementation of `-equiv`. (Side note: the `-` at the start of the function name is a signal that this is a protocol method.) When we compare Date objects, we're calling `-equiv`:

{% highlight clojure %}
(extend-type js/Date
  IEquiv
  (-equiv [o other]
    (and (instance? js/Date other)
         (== (.valueOf o) (.valueOf other))))
{% endhighlight %}

We can see that, in the end, we're comparing values via `.valueOf`.

But, if we try to compare dates via `<` or `>`:

{% highlight clojure %}
cljs-user => (> (js/Date. "September 15, 2015") (js/Date. "September 14, 2015")))
WARNING: cljs.core/>, all arguments must be numbers, got [js/Date js/Date] instead.
true
{% endhighlight %}

The comparison functions `<`, `<=`, `>`, and `>=` are actually macros that check types. They expect numeric values and will provide a warning if other types are given. In the end, the comparisons are simply JavaScript:

{% highlight clojure %}
(core/defmacro ^::ana/numeric >
  ;;other arities omitted...
  ([x y] (bool-expr (core/list 'js* "(~{} > ~{})" x y))))
{% endhighlight %}

For those interested in the cljs compiler, following `::ana/numeric` to figure out how the type checks are done might be a worthwhile endeavor.

[cljs-time]: https://github.com/andrewmcveigh/cljs-time
