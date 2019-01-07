---
title: How to make remote calls in CLJS
tags: how-to language-learning
---
<script type="text/javascript" src="/js/remote_call.js"></script>

> "I have my data ready to go. How do I get it to clojurescript?"

If you've been focused on backend work, it can be confusing when you get started on the frontend. One of the first steps is getting your data from the backend to the frontend.

[`cljs-http`][cljs-http] is a nice library that makes it simple to get your data and move on to what you are really trying to accomplish.

Let's say you have the following JSON on your server, located at `/resources/clojure-langs.json`:

{% highlight javascript %}
{"languages":
  [{"name": "clojure", "host-language": "java"},
   {"name": "clojurescript", "host-language": "javascript"},
   {"name": "clojure-clr", "host-language": "c#"}]}
{% endhighlight %}

Making a simple remote call to the server only takes a few lines:

{% highlight clojure %}
(ns cljs-made-easy.remote-calls
  (:require [cljs-http.client :as http]
            [cljs.core.async :refer [<!]])
  (:require-macros [cljs.core.async.macros :refer [go]]))

(defn make-remote-call [endpoint]
  (go (let [response (<! (http/get endpoint))]
        ;;enjoy your data
        (js/console.log (:body response)))))

;;this could be a static file or an endpoint that generates the JSON
(make-remote-call "/resources/clojure-langs.json")
{% endhighlight %}

Here's a live example, retrieving the static JSON from my server:
<div>
  <button id="output-button" onclick="cljs_made_easy.core.getJSON()">Retrieve JSON</button>
  <pre id="output"></pre>
</div>

Simple `get` and `post` will get you a long way, and the docs provide great examples for the times you need more options.

[cljs-http]: https://github.com/r0man/cljs-http
