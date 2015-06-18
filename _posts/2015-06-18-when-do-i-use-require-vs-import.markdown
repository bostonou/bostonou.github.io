---
title: When do I use :require vs :import?
---

Following up my [last post on Google Closure][closure-post], I wanted to give some pointers on including the library in your code. When you include the lib, it's sometimes unclear when to use `:require` vs `:import`.

In [David Nolen's ClojureScript 101][cljs-101] post, he says:

> Here we use :import so that we can use short names for the Google Closure constructors.
>
> Note: :import is only for this use case, you never use it with ClojureScript libraries

If you look at the cljs analyzer, you'll see the following error message in [`parse-import-spec`][parse-import]:

{% highlight clojure %}
(throw (error env
  (parse-ns-error-msg spec "Only lib.ns.Ctor or [lib.ns Ctor*] spec supported in :import")))
{% endhighlight %}

We can see that the `:import` spec expects some type of constructor (referred to as `Ctor` in the error message). As an example, we can revisit [cljs-http][cljs-http] and look at the ns for [cljs-http.util][util]:

{% highlight clojure %}
(ns cljs-http.util
  (:import goog.Uri) ;;<-- Closure Library
  (:require [clojure.string :refer [blank? capitalize join split lower-case]]
            [cognitect.transit :as t]
            [goog.userAgent :as agent] ;;<-- Closure Library
            [no.en.core :refer [base64-encode]]))
{% endhighlight %}

The docs for [`goog.Uri`][goog-uri-docs] show that `Uri` is a class, so its constructor is loaded via `:import`:

{% highlight clojure %}
;;(:import goog.Uri)

(defn build-url
  "Build the url from the request map."
  [{:keys [scheme server-name server-port uri query-string]}]
  (str (doto (Uri.) ;; <-- Constructor
         (.setScheme (name (or scheme :http)))
         (.setDomain server-name)
         (.setPort server-port)
         (.setPath uri)
         (.setQuery query-string true))))
{% endhighlight %}

[`goog.userAgent`][user-agent-docs] is a namespace that provides several functions, so it's loaded via `:require`:

{% highlight clojure %}
;;(:require [goog.userAgent :as agent])

(defn user-agent
  "Returns the user agent."
  [] (agent/getUserAgentString)) ;;<-- simple fn call
{% endhighlight %}

One other point of confusion is Enums (e.g. [`goog.events.EventType`][event-type-enum]). Enums are [implemented as js objects][enum-implementation]; we `:import` them as constructors:

{% highlight clojure %}
(ns cljs-made-easy.example.core
  (:require [goog.events :as events]))
  (:import [goog.events EventType]))

(events/listen! src EventType.CLICK callback) 
{% endhighlight %}

I've seen enums used as `EventType.ACTION` and `EventType/ACTION`, and both appear to work. `EventType.ACTION` is used more frequently in my experience, but there doesn't seem to be an idiomatic version.

So here's your guideline: **use `:import` for Closure classes and enums, and use `:require` for everything else.**

[enum-implementation]: http://google.github.io/closure-library/api/source/closure/goog/events/eventtype.js.src.html#l45
[event-type-enum]: http://google.github.io/closure-library/api/enum_goog_events_EventType.html
[user-agent-docs]: http://google.github.io/closure-library/api/namespace_goog_userAgent.html
[goog-uri-docs]: http://google.github.io/closure-library/api/class_goog_Uri.html
[closure-post]: http://clojurescriptmadeeasy.com/blog/do-not-forget-about-google-closure.html
[cljs-101]: http://swannodette.github.io/2013/11/07/clojurescript-101/
[parse-import]: https://github.com/clojure/clojurescript/blob/r3308/src/main/clojure/cljs/analyzer.cljc#L1399-L1412
[cljs-http]: http://clojurescriptmadeeasy.com/blog/how-to-make-remote-calls.html
[util]: https://github.com/r0man/cljs-http/blob/master/src/cljs_http/util.cljs

