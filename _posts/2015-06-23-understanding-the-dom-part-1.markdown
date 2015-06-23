---
title: Understanding the DOM, Part 1
---

The DOM (pronounced like "Mom" or "Dome" depending on who you talk to) is one of the basic building blocks to understand when it comes to web development. [Mozilla provides a good definition][dom-defined]:

> The Document Object Model (DOM) is a programming interface for HTML, XML and SVG documents. It provides a **structured representation** of the document (a tree) and it defines a way that the structure can be accessed from programs so that they can change the document structure, style and content. The DOM provides a **representation** of the document as a structured group of nodes and objects that have properties and methods. **Nodes can also have event handlers attached to them**, and once that event is triggered the event handlers get executed. Essentially, it connects web pages to scripts or programming languages. *(my emphasis added)*

I've emphasized the two key points:

1. The DOM is provides a structured representation of the document (e.g. HTML page)
2. The nodes of the DOM can have event handlers attached to them

Here's a simple example that shows a typical type of DOM interaction, using a [cljs wrapper of jQuery][jayq]:

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

<span id="example-element"><u>Here's our example text. Click me and I change colors.</u></span>

If you right-click on the example text and select "Inspect Element" from the context menu, you'll see the node (also called an element) in the DOM. Specifically, it is a `span` with an id of `example-element`. Because the DOM is "structured" (a tree), we can navigate to any node within the tree.

Our code gets the node with id `example-element`, then adds `click-handler` as an event handler. Whenever you click the text, a `click` event occurs and our `click-handler` is called. We first get the node, then toggle its color between red and blue.

> Element ids SHOULD be unique on a given page. The [spec][html-spec] says ids MUST be unique but browsers tend to be more accepting than they should. If you give multiple elements the same id, your code will probably work but your team will never want to work with you again. 

The code above is pretty common, but it's also Wrong™. We've all been writing Javascript that way for a long time, and we've gotten really far with it. But it's still Wrong™. Can you figure out why*? (Hint: What does the DOM provide vs how we're using it?)

We'll fix the code in the next post.

_\* It's not about code structure, using jQuery vs Google Closure, etc. It's about principles._

<script type="text/javascript" src="/js/jquery_toggle.js"></script>

[jayq]: https://github.com/ibdknox/jayq
[dom-defined]: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model
[html-spec]: http://www.w3.org/TR/html401/struct/global.html#h-7.5.2
