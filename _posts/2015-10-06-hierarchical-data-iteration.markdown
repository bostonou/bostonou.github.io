---
title: JS to CLJS - Hierarchical Data Iteration
---

> From time to time I'll see a post on JavaScript and take some time to work it out in ClojureScript. This hopefully will take away some of the Magic&#8482; from functional programming and show how to solve real problems. I promise you won't have to learn what a monad is.

[Derick Bailey][derick-twitter] walks through [iterating through heirarchical data][site-post] and uses [Generators][generators] to help clean up the code. His example is a great example to show the simplicity of cljs.

### Problem

We have some hierarchical data (nested maps and vectors):

{% highlight clojure %}
(def job-data
  {:_id "56044a42a27847d11d61bfc0"
   :schedule-template-id "55099ebdcca58a0c717df912"
   :jobs [{:job-template-id "55099ebdcca58a0c717df91f"
           :_id "56044a42a27847d11d61bfd5"
           :step-templates [{:job-step-template-id "55099ebdcca58a0c717df921"
                             :_id "56044a42a27847d11d61bfd9"}
                            {:job-step-template-id "55099ebdcca58a0c717df920"
                             :_id "56044a42a27847d11d61bfd7"}]}
          {:job-template-id "55099ebdcca58a0c717df91c"
           :_id "56044a42a27847d11d61bfd0"
           :step-templates [{:job-step-template-id "55099ebdcca58a0c717df91d"
                             :_id "56044a42a27847d11d61bfd3"}]}
          {:job-template-id "55099ebdcca58a0c717df919"
           :_id "56044a42a27847d11d61bfcb"
           :step-templates [{:job-step-template-id "55099ebdcca58a0c717df91a"
                             :_id "56044a42a27847d11d61bfce"}]}
          {:job-template-id "55099ebdcca58a0c717df916"
           :_id "56044a42a27847d11d61bfc6"
           :step-templates [{:job-step-template-id "55099ebdcca58a0c717df917"
                             :_id "56044a42a27847d11d61bfc9"}]}
          {:job-template-id "550aede1cca58a0c717df927"
           :_id "56044a42a27847d11d61bfc1"
           :step-templates [{:job-step-template-id "550aedebcca58a0c717df929"
                             :_id "56044a42a27847d11d61bfc4"}]}]})
{% endhighlight %}

Given an `_id` of a `step-template`, we need to return the `step-template` map.

### Step 1 - Find the step-template from sequence of step-templates

The most basic useful step is searching a sequence of step-templates for the id.

{% highlight clojure %}
(defn find-step-in-templates [step-id steps]
  (first (filter (fn [step] (= step-id (:_id step))) steps)))
{% endhighlight %}

This says "`filter` out `steps` that don't have `:_id` equal to `step-id`. Also, I only want the `first` one."

* We only expect there to be one step that matches, so we just want the `first` match.
* `filter` is lazy, meaning that after we get our first step, the code won't keep filtering.
* If there are no matching steps, we'll get `nil`.

### Step 2 - Finding the step-template from the job

Now, with our basic step complete, we just need to use that basic step for the job as a whole:

{% highlight clojure %}
(defn find-step [step-id jobs]
  (first (keep (fn [job]
                 (find-step-in-templates step-id (:step-templates job)))
               jobs)))
{% endhighlight %}

This says "keep only the `step`s where we find a matching step in the `:step-templates` of a `job`. Also, I only want the `first` one."

* We only expect there to be one step that matches, so we just want the `first` match.
* `keep` is lazy, meaning that after we get our first step, the code won't keep processing the rest of the sequence.
* If there are no matching steps, we'll get `nil`.

### Test it out 
{% highlight clojure %}
cljs.user => (find-step "56044a42a27847d11d61bfd9" (:jobs job-data))
=> {:job-step-template-id "55099ebdcca58a0c717df921",
    :_id "56044a42a27847d11d61bfd9"}

cljs.user => (find-step "56044a42a27847d11d61bfd3" (:jobs job-data))
=> {:job-step-template-id "55099ebdcca58a0c717df91d",
    :_id "56044a42a27847d11d61bfd3"}

cljs.user => (find-step "invalid-id" (:jobs job-data))
=> nil
{% endhighlight %}

### Benefits

To solve this, we just use the stuff we always use: `filter`, `keep`, `first`, and data. There's nothing special about our solution. **This is real "code reuse" that I find valuable.** We can use these same functions to process data throughout our application, whether it's configuration or JSON coming over the wire or data coming back from the database. It's just data and we have awesome, simple building blocks for working with data.

We're also thinking almost 100% about the problem. "Find the step in a group of step templates" and "Find the step template in a group of groups of step templates". That's what the problem is and our code follows exactly that. The only thing outside the problem is performance related: does the code keep running after we've found our first solution? We know it doesn't because of [laziness][laziness], so we have our answer. But it's not a question that gets in the way of solving the problem.

So with ClojureScript, we get code that follows the problem and code that's reusable everywhere. And while Generators look pretty cool, cljs gives us what we need from a very stable API; we're not required to keep up with the "new" hotness and continually change the way we solve problems.

[derick-twitter]: https://twitter.com/derickbailey
[site-post]: http://derickbailey.com/2015/10/05/using-es6-generators-to-short-circuit-hierarchical-data-iteration/
[generators]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators
[laziness]: http://clojurescriptmadeeasy.com/blog/how-to-follow-recursion-with-lazy-sequences.html
