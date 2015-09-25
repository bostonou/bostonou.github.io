---
title: How to get the (distinct) difference between two lists
---

From time to time I'll see a post on JavaScript and take some time to work it out in ClojureScript. This helps me in a few ways:

* I get more practice in cljs
* I validate (or invalidate) my love for cljs
* I get more insight into how to *sell* cljs :)

I recently ran across [Derick Bailey's site][derick-site] and decided to rework some of his posts in cljs. The first post walks through [getting the difference between two arrays, as a distinct list][site-post]. The requirements are:

* We have a full list of valid values.
* We're given a non-distinct list of both valid and invalid values.
* Find a distinct list containing only the valid values.

Derick's thought process comes to "I wish I had a Set and a simple way to get the difference between two lists." Well, that's pretty nice because we have exactly that with cljs.

### Sets

`set`s are basically a "bag of values, where each value only appears once". The "bag" part is important; it basically means there is no defined order to sets.

{% highlight clojure %}
cljs.user=> (set [1 1 1])
#{1} ;; #{} is the set literal syntax

cljs.user=> (= #{1} (set [1 1 1]))
true

cljs.user=> #{1 1}
"clojure.lang.ExceptionInfo: Duplicate key: 1..."
;; If you use the literal syntax, it must be a valid set

cljs.user=> (nth #{1 2 3} 1)
"Error: nth not supported on this type cljs.core/PersistentHashSet..."
;; Remember, no order to sets.
{% endhighlight %}

### Solution

So let's define our values as:

{% highlight clojure %}
cljs.user=> (def valid-values [1 3 2 3 2 4 1 3 5])

cljs.user=> (def given-values [1 3 2 :invalid 3 1 :another-invalid])
{% endhighlight %}

Finding our distinct values are simple one-liners thanks to cljs.core:

{% highlight clojure %}
cljs.user=> (set valid-values)
#{1 3 2 4 5}

cljs.user=> (set given-values)
#{1 3 2 :invalid :another-invalid}
{% endhighlight %}

Selecting only the given valid values? We want the values that ONLY occur in both sets, also know as the `intersection`:

{% highlight clojure %}
cljs.user=> (require 'clojure.set) ;;require set lib, part of cljs core

cljs.user=> (clojure.set/intersection (set valid-values) (set given-values))
#{1 3 2}
{% endhighlight %}

As a bonus, what if we wanted all of the invalid values? Then, we want the valid values removed from the given-values:

{% highlight clojure %}

cljs.user=> (clojure.set/difference (set given-values) (set valid-values))
#{:invalid :another-invalid}
{% endhighlight %}

Working through this post is a good reminder to continually work on expanding your knowledge of core functions. It's not necessary to have every fn and it's docs memorized, but it's very handy to face a problem and have a good feel for what's already provided.

### Additional Notes

If you want order with sets, simply turn it into a `seq` first. Calling `seq` on the same `set` will always give the same order:

{% highlight clojure %}
cljs.user=> (seq #{2 3 1}) ;;not sure what the order will be
(1 3 2) ;;it will always be this order

cljs.user=> (nth (seq #{2 3 1}) 0)
1
{% endhighlight %}

The lib is `clojure.set` in both cljs and clj. I believe this was a lib that was ported to cljs early and the naming hadn't been decided on yet. Most cljs libs are `cljs.lib`, but some `clojure.lib` are still around (e.g. `clojure.string`).

[derick-site]: http://derickbailey.com/
[site-post]: http://derickbailey.com/2015/09/23/how-to-get-the-difference-between-two-arrays-as-a-distinct-list-with-javascript-es6/
