---
title: What does `#js` mean?
---

One of the best ways to up your cljs game is to read other people's code. When I first got started reading/writing cljs, I was confused when I saw `#js`.

First of all, `#js` is a *reader literal*. Reader literals are used by the cljs reader and can be used to read in a specific data type. Consider a javascript array:

{% highlight clojure %}
=> (require '(clojure.reader :as reader])

=> (array 1 2 3)
#js [1 2 3] ;;a javascript array

=> (type (reader/read-string "#js [1 2 3]"))
#<function Array() { [native code] }>
{% endhighlight %}

You'll see `#js` when doing JS interop. For example, [Om][om] provides a cljs wrapper of [React][react], and you can see `#js` used to pass proper javascript objects to React:

{% highlight clojure %}
(dom/button
  ;;create a javascript object
  #js {:onClick
       #(do
          (println "I can read!" (:foo data))
          (om/update-state! owner :count inc))}
  "Bump!")
{% endhighlight %}

One thing to note is that the literal only applies at the top level (i.e. won't deeply apply to nested structures):
{% highlight clojure %}
;;WRONG - probably wanted js objects all the way down
#js {:jsObj {:not-a :object}}

;;CORRECT - js objects all the way down
#js {:jsObj #js {:proper :object}}
{% endhighlight %}

cljs actually includes four built-in literals: `#inst`, `#js`, `#queue`, and `#uuid`.

{% highlight clojure %}
=> (js/Date.)
#inst "2015-06-09T05:38:26.146-00:00"
=> (type (reader/read-string "#inst \"2015-06-09T05:38:26.146-00:00\""))
#<function Date() { [native code] }>

=> (into cljs.core.PersistentQueue.EMPTY [1 2 3])
#queue [1 2 3]
=> (type (cljs.reader/read-string "#queue [1 2 3]"))
cljs.core/PersistentQueue

=> (UUID. "random-string-here") ;; cljs-version <= 0.0-3269
#uuid "random-string-here"
=> (uuid "random-string-here") ;; cljs-version >= 0.0-3308
#uuid "random-string-here"
=> (type (reader/read-string "#uuid \"random-string-here\""))
cljs.core/UUID
{% endhighlight %}

Now, when you see a reader literal, you'll understand exactly what you're looking at. If you're interested in the implementation details, [check out the source][tagged-literals].

[om]: https://github.com/omcljs/om/blob/master/examples/hello/src/core.cljs
[react]: https://facebook.github.io/react/
[tagged-literals]: https://github.com/clojure/clojurescript/blob/r3308/src/main/clojure/cljs/tagged_literals.clj
