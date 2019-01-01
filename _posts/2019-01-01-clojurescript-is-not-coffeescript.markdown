---
title: ClojureScript Is Not CoffeeScript
---

>  A lot of people have the scars of switching over to CoffeeScript and then finding they had a codebase in an obscure language which had been abandoned by the hipsters. - [A scarred JavaScript Developer][scar-comment]

We've all felt the pain of investing time and energy into learning some tech or library, only to later abandon that choice in favor of the Newer, Better, and Faster™. We're lucky if it's only a new library, practically starting over if it's a new framework, and probably looking for a new job if it's a new language.

With the stakes that high, it's certainly a foolish decision to choose an "obscure language" like ClojureScript, right? Why would we expect ClojureScript to be any different than CoffeeScript?

## ClojureScript is a Lisp

Lisp is 60 years old and has proven value and staying power while a language like CoffeeScript is relatively young. And when CoffeeScript's golden rule is "It's just JavaScript," it's reasonable to expect JavaScript to borrow the ideas that made CoffeeScript valuable in the first place. CoffeeScript can be rendered unnecessary *because* it is so close to plain JavaScript.

ClojureScript is significantly different as a lisp, and it's unlikely that JavaScript will ever provide the value that developers find in a lisp.

## ClojureScript has Clojure

Clojure proper is really a language that targets different runtimes: Clojure for Java, ClojureScript for JavaScript, and ClojureCLR for the CLR. The makeup of Clojure developers actually spans multiple development communities.

If anyone were to create a language to supplant ClojureScript, they would simultaneously need to overcome the value provided by Clojure and ClojureCLR. In other words, to convince me to choose some new (or old) language over ClojureScript, you'd also have to provide an equally compelling story for Clojure and ClojureCLR.

Additionally, each community benefits from the existence of the other. If David Nolen et al. decided to abandon their work on ClojureScript, it would be in the interest of the Clojure community to continue the work. **The existence of Clojure makes the death of ClojureScript much more unlikely.**

## ClojureScript is a Stable Language

Part of Lisp's value is that the language is tiny. ["Many of the core constructs of Clojure are not built-in primitives but macros just like users can define."][clojure-core] That means that there just isn't much to the core, so there isn't a large surface area for things to change.

Most of the innovation occurs in libraries. Because of that, ClojureScript developers are free to choose if and when to adopt a new thing. They're also free to implement new features that would be core changes to other languages (e.g. [core.async][core-async]). And if we make a bad choice, it's a library that can be deprecated instead of a core language feature that we're stuck with forever.

## ClojureScript has a Stable API

In a world of JavaScript Fatigue and constant rewrites from the ground up, ClojureScript's API has been stable for years. You learn the data structures and API for working with them and reap the benefits of that investment for years.

[Rich Hickey talks about breakage vs growth][spec-ulation] as a more precise way to think about change. Relaxing requirements is growth, i.e. change that is safe for our users. Stricter requirements and removal is breakage, i.e. change that breaks what we provide for users. Having this design philosophy means that we're not constantly abandoning code due to incompatible new features. While frameworks and libraries change, even in the ClojureScript world, code written from today looks very similar to code written years ago.

At some point, developers get tired of spinning our wheels learning "new" things that are just different packaging over the same principles. It's part of the reason that, on average, [the most experienced JavaScript developers choose ClojureScript][js-experience]. After learning dozens of libraries and frameworks that all have their own spin on the same thing, you begin to value a stable foundation that simply lets you get stuff done.

---

As professionals, we are obligated to make decisions based on the needs of our customers and stakeholders. When it comes to explaining ClojureScript and the risk of ending up with a dead language, we have a very good story to show it's a low risk decision.

[scar-comment]: https://hackernoon.com/functional-programming-in-javascript-is-an-antipattern-58526819f21e
[clojure-core]: https://clojure.org/about/lisp
[core-async]: https://github.com/clojure/core.async
[spec-ulation]: https://www.youtube.com/watch?v=oyLBGkS5ICk
[js-experience]: https://2018.stateofjs.com/javascript-flavors/overview/tools-years-of-experience
