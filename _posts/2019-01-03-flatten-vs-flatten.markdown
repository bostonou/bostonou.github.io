---
title: Flatten vs. Flatten&#58; An Example In Incidental Complexity
tags: api evaluation cljs-value
---

*This is not a post to bash JavaScript, library authors, or those of us who use and enjoy JavaScript. It's an attempt to step back and take an honest look at what can be better. Thanks to all who have put in significant time and effort to build the tools I often take for granted.*

{% highlight javascript %}
//Lodash
_.flatten([[1, 2, [3]]]); //[1, 2, [3]]

//Underscore
_.flatten([[1, 2, [3]]]); //[1, 2, 3]
{% endhighlight %}

These few easy lines of code provide a rich example of the unnecessary stress and overhead we place on ourselves as developers.

### JavaScript Fatigue

If you already know Lodash and then switch to Underscore, you're essentially relearning a new, slightly different way of accomplishing the same things. I hold that **JavaScript fatigue is not because we tire of learning, but because we tire of learning with no purpose or added value**. Neither code example is necessarily right or wrong, but knowing the difference between these two lines of code adds zero value to our work and is just a waste of time and energy.

### Incidental Complexity

Whenever a developer has to switch between these two lines of code, they always have to make an extra mental note of which version of `flatten` is being used. While using Underscore and Lodash right next to each other may not be the norm, similar examples exist in JavaScript (e.g. `slice` and `splice` return the same thing, but one mutates and one doesn't. Which is which without looking at the docs?). This is 100% work that we've created for ourselves and not part of the inherent complexity of the problems we're trying to solve.

### Native Functions

And then there's the [You-Dont-Need-Lodash-Underscore repo][no-repo], which helps you decide if you really need Lodash/Underscore or if you can use native functions. I'm sure a lot of smart, well-intentioned people have worked on that repo, but isn't this all just a bunch of work concerning complexity that we shouldn't have to think about?

`flatten` is not special, it's not particularly significant to my work, and it's a straightforward function. Why do I need to think about dependencies, what's native, and different return values based on which library I choose? And why should I need to consider this complexity for many of the simple functions I use?

This is significant extra overhead that **isn't related to what I'm actually trying to do**. It's striking to me how many of us just accept this as how development has to be.

### A Better Way

One of ClojureScript's biggest value offers is a consistent, stable API. Every problem I mentioned above is just not really a concern when I'm writing ClojureScript. `flatten` is just there with no extra dependencies. I learn what it does and then never think much about it again. When I'm working on my problem, I'm working on my problem and never spend a single moment considering which version of `flatten` is in play.

ClojureScript as a language and community isn't perfect here. Like any language, it has some points of [cognitive overhead][property-access] and its [share][dont-like-1] of [warts][dont-like-2]. But once you sit down and work with ClojureScript's API, you begin to recognize how much it cuts through much of the incidental complexity we've accepted as a normal part of our work.

[no-repo]: https://github.com/you-dont-need/You-Dont-Need-Lodash-Underscore
[property-access]: http://clojurescriptmadeeasy.com/blog/js-interop-property-access.html
[dont-like-1]: https://www.reddit.com/r/Clojure/comments/5zq45b/what_are_the_things_that_you_dont_like_in_clojure/
[dont-like-2]: https://www.reddit.com/r/Clojure/comments/650hw1/what_are_the_biggest_pain_points_of/
