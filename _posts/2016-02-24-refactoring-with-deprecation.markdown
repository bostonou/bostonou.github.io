---
title: Cleaning up old code with :deprecated
tags: how-to language-learning
---

For any long-lived application, there comes a time when code needs to be refactored. It's often the case that we don't have the time or energy to do so right then, so we're left with a few options. We can either add a comment, update a few doc strings, or rename functions to `bad-idea-do-not-use-anymore`.

Something I've started doing recently is using the `:deprecated` metadata. I prefer this to a doc string or function rename for a couple of reasons:

* It's easy to add without much work on my part. I don't have to get out of the flow of what I'm doing, it's a minimal code change, and it's easy for other devs on the team to see that the function shouldn't be used anymore.

* The compiler can do the work of telling me where and how much refactoring I need to do. By default, the compiler will give warnings for any used deprecated functions. We can turn off warnings by using the `:deprecation-nowarn` version or setting [`{:fn-deprecated false}`][compiler-options] in our compiler options.

In the clojure source, the metadata includes a version, e.g. [`{:deprecated "1.2"}`][clj-deprecated]. For an application, since I'm less worried about versions and more concerned with calling out code that needs to be changed, I tend to just use the shorthand `^:deprecated`.

Since Figwheel by default won't reload code with warnings, I'll set up two builds: one with warnings turned off and one with warnings turned on. If I have a lot of refactoring to do that's unrelated to my current development, I'll develop with deprecation warnings turned off. Then, when I'm ready to handle deprecated code, I'll switch to the build with warnings enabled.

{% highlight clojure %}
;;details elided
{:builds
  [{:id "dev"
    :compiler {:warnings {:fn-deprecated false}}}
   {:id "refactor"
    :compiler {:warnings {:fn-deprecated true}}}]} ;;defaults to true
{% endhighlight %}

More details can be found in [this patch][patch].

[clj-deprecated]: https://github.com/clojure/clojure/blob/clojure-1.7.0/src/clj/clojure/core.clj#L2126-L2127
[compiler-options]: https://github.com/clojure/clojurescript/wiki/Compiler-Options#warnings
[patch]: http://dev.clojure.org/jira/browse/CLJS-371
