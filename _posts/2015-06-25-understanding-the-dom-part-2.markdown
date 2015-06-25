---
title: Understanding the DOM, Part 2
---

In the [last post][last-post], we left off with a question. What's wrong with this code?

{% highlight clojure %}
(ns cljs-made-easy.core
  (:require cljsjs.jquery
            [jayq.core :refer [$]]))

(defn click-handler [_e]
  ;;($ #my-id) === get the element with id "my-id"
  (let [$element ($ :#example-element)
        red "rgb(255, 0, 0)"]
    ;;toggle the color of the text
    (if (= red (.css $element "color"))
      (.css $element "color" "blue")
      (.css $element "color" "red"))))

;;add event handler; when the element is clicked call `click-handler`
(.click ($ :#example-element) click-handler)
{% endhighlight %}

The problem is this line:

{% highlight clojure %}
(if (= red (.css $element "color")) ...)
{% endhighlight %}

Remember that the DOM is for **representation**. Here, we're using the DOM to manage state, as if it were a database. We could easily imagine this code being `SELECT color FROM DOM WHERE id="example-element";`.

If we treat the DOM properly and use Clojurescript's built in state management, we can start to separate these conflated issues.

{% highlight clojure %}
(ns cljs-made-easy.core
  (:require cljsjs.jquery
            [jayq.core :refer [$]]))

(def state (atom {}))

;;logic for transitioning state
(defn- update-color [{:keys [color] :as state}]
  (if (= "red" color)
    (assoc state :color "blue")
    (assoc state :color "red")))

(defn click-handler [_e]
  (let [$element ($ :#example-element)]
    ;;transition our state
    (swap! state update-color)
    ;;render based on state
    (.css $element "color" (:color @state))))

;;add event handler; when the element is clicked call `click-handler`
(.click ($ :#example-element) click-handler)
{% endhighlight %}

We keep our state in an `atom` and transition the state as needed. This is a completely separate concern from rendering. What does that get us?

* The logic for managing state is easier to recognize and understand. It is simple.
* We can easily test `update-color` (our state management) without any dependency on the DOM.
* It's faster. Interacting with the DOM is SLOW; we get a major performance win anytime we avoid it.
* If we change our representation layer (e.g. to iOS/Android), we're not touching our state logic.

So here's your guideline: **Don't query the DOM.**

If you don't query the DOM, you'll be forced to use other facilities for state management, which is exactly what we want.

Thanks to [Cognitect][cognitect] for [Pedestal App][pedestal] and Facebook for [React][react]. Pedestal (no longer actively developed) was the first library to help me see this and React really helped me grok it.

[last-post]: http://clojurescriptmadeeasy.com/blog/understanding-the-dom-part-1.html
[cognitect]: http://cognitect.com/
[pedestal]: https://github.com/pedestal/pedestal-app
[react]: http://facebook.github.io/react/
