---
title: How to follow recursion with lazy sequences
tags: how-to language-learning
---
Recursion is often the most difficult programming concept to become comfortable with. Add laziness and it can make it hard to even understand what's going on. Stepping through examples is great practice for really grasping these fundamental concepts.

In Programming Clojure, the authors give a nice, concise (though admittedly inefficient)  definition of a Fibonacci sequence:

{% highlight clojure %}
; holds the head (avoid!)
(def head-fibo (lazy-cat [0N 1N] (map + head-fibo (rest head-fibo))))
{% endhighlight %}

The execution detail is hard to follow, so let's step through to see what's happening.

The first thing to do is understand what [`lazy-cat`][lazy-cat] does:

{% highlight clojure %}
(lazy-cat xs ys zs) === (concat (lazy-seq xs) (lazy-seq ys) (lazy-seq zs))
{% endhighlight %}

And [`lazy-seq`][lazy-seq]?

> ...yields a Seqable object that will invoke the body only the first time seq is called

In other words, the args you pass to `lazy-seq` won't be evaluated until they are needed.

So let's work out the first couple of simple steps:

{% highlight clojure %}
(take 1 head-fibo)
(take 1 (lazy-cat [0N 1N] (map + head-fibo (rest head-fibo))))
(take 1 (concat (lazy-seq [0N 1N])
                (lazy-seq (map + head-fibo (rest head-fibo)))))
;; => (0N)
{% endhighlight %}

Since we `take 1`, the `0N` of the first `lazy-seq` suffices and we don’t need to further evaluate, we get the value `(0N)`.

`take 2` is similar, in that the first `lazy-seq` suffices by providing two values, giving `(0N 1N)`.

`take 3` gives us a more interesting case:

{% highlight clojure %}
(take 3 head-fibo)
(take 3 (lazy-cat [0N 1N] (map + head-fibo (rest head-fibo))))
(take 3 (concat (lazy-seq [0N 1N])
                (lazy-seq (map + head-fibo (rest head-fibo)))))
{% endhighlight %}

Now, the first `lazy-seq` only satisfies 2 out of the 3 values needed, so we process the next `lazy-seq`:

{% highlight clojure %}
(take 3 (concat (lazy-seq [0N 1N])
                (lazy-seq (map +
                               (lazy-cat [0N 1N]
                                         (map + head-fibo (rest head-fibo)))
                               (rest (lazy-cat [0N 1N]
                                               (map + head-fibo (rest head-fibo))))))))
(take 3 (concat (lazy-seq [0N 1N])
                (lazy-seq (map +
                               (concat (lazy-seq [0N 1N])
                                       (lazy-seq (map + head-fibo (rest head-fibo))))
                               (rest (concat (lazy-seq [0N 1N])
                                             (lazy-seq (map + head-fibo (rest head-fibo)))))))))
{% endhighlight %}

Since we need one more value, we map over the first value of each `lazy-seq`, namely `0N` and `1N`, giving:

{% highlight clojure %}
(take 3 (concat (lazy-seq [0N 1N])
                (lazy-seq (map +
                               0N
                               1N))))
;; => (0N 1N 1N)
{% endhighlight %}

That gives us our final value of `(0N 1N 1N)`.

Becoming comfortable with recursion and laziness takes time and practice, but it DOES become second nature if you're willing. And soon, you'll find yourself writing more elegant code with fewer bugs.

[lazy-cat]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/lazy-cat
[lazy-seq]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/lazy-seq
