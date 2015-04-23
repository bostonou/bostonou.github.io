---
title: Why use an atom in a single-threaded environment?
---
The [Clojure docs for atoms][atoms] reference concepts like threads, atomic changes, and spin loops. Since those don't apply to a single-threaded environment, why would you ever use an atom?

Atoms provide a way to manage shared, synchronous, independent state.

In a single-threaded environment like the browser, it's idiomatic to store all application state in an atom. All updates to the application state are done via [`swap!`][swap!] and [`reset!`][reset!]. Then, your functions for generating the DOM can deref the atom to get the relevant state.

[atoms]: http://clojure.org/atoms
[swap!]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/swap!
[reset!]: http://clojure.github.io/clojure/clojure.core-api.html#clojure.core/reset!
