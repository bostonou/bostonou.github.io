---
title: How to `apply` with the console api
tags: language-learning how-to interop
---

Over in the [cljs slack channel][slack], a question came up about using `apply` with `js/console.log`.

{% highlight clojure %}
=> (apply js/console.log ["log" "me"])
;; throws Uncaught TypeError: Illegal invocation
{% endhighlight %}

Using [cljs.core/enable-console-print!][print] as an example, we see the proper way:

{% highlight clojure %}
=> (.apply (.-log js/console) js/console (into-array ["log" "me"]))
;; logs "log me"
{% endhighlight %}

This works with each of the [console methods (e.g. warn, error, etc.)][console-api]. Additionally, check out [shodan][shodan], which provides wrappers for the console api.

### Why doesn't \`apply\` work?

*What follows is just some exploration; it isn't necessary to understand and (unfortunately) doesn't provide a solution. I used Chrome for this exploration.*

When `apply` is called with a JavaScript function, you generally end up at this branch:

{% highlight clojure %}
(defn apply
  ([f args]
     (let [fixed-arity (.-cljs$lang$maxFixedArity f)]
       (if (.-cljs$lang$applyTo f)
         (let [bc (bounded-count args (inc fixed-arity))]
          (if (<= bc fixed-arity)
            (apply-to f bc args)
            (.cljs$lang$applyTo f args)))
         ;;You hit this branch
         (.apply f f (to-array args)))))
  ...)
{% endhighlight %}

So our example code ends up as:

{% highlight clojure %}
(apply js/console.log ["log" "me"])

;;compiles to =>
(.apply js/console.log js/console.log (to-array args))

;;should be =>
(.apply js/console.log js/console (to-array args))
{% endhighlight %}

The js method `.apply` expects its first argument to be the `this` value for the function call. So our code passes the actual function as `this`, when we really want the object. Why is `apply` written this way and not considered broken?

`apply` does work in some cases. Static methods work, likely because they don't rely on `this`:

{% highlight javascript %}
Math.max.apply(null,[2,3,1,5,3]); //works
Math.max.apply(Math,[2,3,1,5,3]); //also works
Math.max.apply(Math.max,[2,3,1,5,3]); //Yep; compiled cljs version
Date.UTC.apply(null,[2000,7,3]); //works
Date.UTC.apply(Date,[2000,7,3]); //also works
Date.UTC.apply(Date.UTC,[2000,7,3]); //yep; compiled cljs version

console.log.apply(null,["log","me"]); //doesn't work
console.log.apply(console,["log","me"]); //works
console.log.apply(console.log,["log","me"]); //doesn't work; compiled cljs version
document.createElement.apply(null,["p"]); //doesn't work
document.createElement.apply(document,["p"]); //works
document.createElement.apply(document.createElement,["p"]); //Nope; compiled cljs version
{% endhighlight %}

If we could somehow get the instance from the method call (e.g. get `console` from `console.log`), we could fix cljs `apply` to work in more (all?) cases. But we can't do that in js, so I guess we're kinda sunk. ¯\\\_(ツ)\_/¯

[print]: https://github.com/clojure/clojurescript/blob/r3308/src/main/cljs/cljs/core.cljs#L116
[slack]: http://clojurians.net/
[shodan]: https://github.com/noprompt/shodan
[console-api]: https://developer.mozilla.org/en-US/docs/Web/API/Console
