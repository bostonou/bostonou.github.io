---
title: Is there a canonical cljs reference?
tags: language-learning
---

cljs is improving at a really great pace. Unfortunately, that means great blog posts can quickly go stale and leave out some new, useful functionality. Surely cljs would benefit from a nice, official reference that was a well-known starting point for aspiring (and always improving) cljs devs.

Thankfully, [Shaun Lebron][shaun-twitter] (and others) has started work on creating a great site for cljs, complete with useful, up-to-date documentation. Specifically, the [api-refs][api-refs] are already functional and useful for reference or simply exploring the functionality of cljs.

A really nice addition is the [offline doc-set][dash-doc-set] for [Dash][dash] (or Zeal/Velocity for Linux/Windows). With the doc-set, you get a list of all cljs core functions with:

1. name
2. type (e.g. function or macro)
3. version history
4. docs
5. source code

### A tip on using the docs

When I first started learning clojure, I'd spend time just reading through the [clojure api][clojure-api]. clj/cljs has a lot of built-in functionality that covers a wide range of use cases. More than once, I've started (or finished) implementing something, only to later find out I could have made a simple function call.

True story: Earlier this week, I was talking to a friend about cljs and needing to generate UUIDs. It was strange to me that cljs has a UUID type, but not a built-in fn for generating UUIDs. While randomly looking through the docs, I see that [`random-uuid`][random-uuid] got added to cljs in the latest release.

If you have some time to kill before a meeting, or are looking to be productive on a Friday afternoon, spend some time just reading through the docs. It will go a long way in improving your cljs skills.

[shaun-twitter]: https://twitter.com/shaunlebron
[api-refs]: https://github.com/cljsinfo/api-refs/tree/catalog
[dash-doc-set]: https://github.com/cljsinfo/api-refs/blob/master/README.md#offline-docset-for-dash
[dash]: https://kapeli.com/dash
[clojure-api]: http://clojure.github.io/clojure/
[random-uuid]: https://github.com/cljsinfo/api-refs/blob/catalog/refs/cljs.core_random-uuid.md
