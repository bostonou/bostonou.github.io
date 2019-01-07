---
title: Current and Next Values of a List
tags: how-to cljs-value language-learning
---

Here's a fairly common situation:

1. We have a list of values
2. We need to use each value along with the next value

Generally, you end up with something like this:

{% highlight javascript %}
  var arr = [0, 1, 2, 3];

  //somehow we get our index `i`
  var current = arr[i],
      next = arr[i+1];

  //... do your thing
}
{% endhighlight %}

Whether you use a `for(var i=0; i < arr.length; i++)` or something like [`array.reduce`][array-reduce], you end up having to make sure your index is valid and doesn't reach outside of the array. **We intuitively know the code is brittle and unclear** because we inevitably end up adding a comment that says something like `start with the second one` or `stop at length - 1`.

In cljs, there's a handy-dandy function called [`partition`][partition] that elegantly solves this problem for us.

The most basic version of `partition` only takes `n` and the collection as arguments, splitting the collection into lists of size `n`:

{% highlight clojure %}
cljs.user=> (def items [1 2 3 4 5 6])
[1 2 3 4 5 6]

cljs.user=> (partition 1 items)
((1) (2) (3) (4) (5) (6))

cljs.user=> (partition 2 items)
((1 2) (3 4) (5 6))
{% endhighlight %}

`partition` provides another arity which takes a `step` argument. With `n` and `step`, we can say:

1. Give me a list of `n` items, starting at the first position.
2. Move forward `step` items, then give me the next list of `n` items.
3. Repeat.

So, if we want a list of current value and next value:

{% highlight clojure %}
cljs.user=> (partition 2 1 items)
((1 2) (2 3) (3 4) (4 5) (5 6))
{% endhighlight%}

From there, it's simple to use the current and next value:

{% highlight clojure %}
(for [[current next] (partition 2 1 items)]
  (comment "do your thing"))
{% endhighlight %}

### Additional Notes

When working with `partition`, it's worth knowing about [`partition-by`][partition-by] and [`partition-all`][partition-all] as well. They're similar but handle slightly different cases.

[array-reduce]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce
[partition]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/partition
[partition-by]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/partition-by
[partition-all]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/partition-all

