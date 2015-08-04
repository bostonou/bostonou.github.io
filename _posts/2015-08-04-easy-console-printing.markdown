---
title: Easy console printing
---

If part of your development process includes logging to the console and you get tired of writing `(js/console.log ...)`, consider using `enable-console-print!`.

Simply call `enable-console-print!` when your code is first loaded, and all your `pr`, `pprint`, etc calls will print to the console.

{% highlight clojure %}
(ns cljs-made-easy.core)

(enable-console-print!)

(let [m {:a :map}]
  (js/console.log m) ;; => cljs.core.PersistentArrayMap ...
  (js/console.log (pr-str m)) ;; => {:a :map}
  (pr m)) ;; => {:a :map}
{% endhighlight %}

*Gotcha #1* - If you call print fns before you enable console printing, you'll see an exception: `"No *print-fn* fn set for evaluation environment"`. Make sure `enable-console-print!` is called before any of your print fns.

*Gotcha #2* - Depending on your browser and environment, you may see an exception because the `console` object is not defined (e.g. IE only exposes the `console` object if the developer tools are open). If your production code calls `enable-console-print!` and includes print fns for debugging, you may see prod only bugs. Your best bet is to limit your debugging to development only.

