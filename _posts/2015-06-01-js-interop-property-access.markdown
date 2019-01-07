---
title: JS interop - property access
tags: how-to language-learning interop
---

## Update - July 4, 2017

**The original post is now outdated**. Instead use [`goog.object/get`, `goog.object/getValueByKeys`, and `goog.object/set`][goog object].

Example from the [cljs api][cljs api]:

{% highlight clojure %}
;;instead of aget
(require 'goog.object)
(def obj #js {:foo #js {:bar 2}})

(goog.object/get obj "foo")
;;=> #js {:bar 2} 

(goog.object/getValueByKeys obj "foo" "bar")
;;=> 2

;;instead of aset
(def obj #js {:foo 1})

(goog.object/set obj "foo" "bar")
obj
;;=> #js {:foo "bar"}
 
{% endhighlight %}

See [CLJS-2148][cljs-2148] and [CLJS-2149][cljs-2149] for more details.

<br />
<br />

---

<br />
<br />

## **The rest of this post is now outdated**

[Konrad Garus][reference-post] has a good post explaining the details of accessing js properties from cljs. It's worth your time to read the entire post, but here are some general guidelines.

## Use `.-property` for cljs

{% highlight clojure %}
(.-property obj) ;;get property
(set! (.-property obj) v) ;;set property
 
{% endhighlight %}

In the normal case, you'll want to use `.-property` to access js properties. In the [line-reader example][line-reader], we use `.-_lastLineData` to get/set data on `this`.

{% highlight clojure %}
(defn- transform [chunk encoding done]
  (this-as this
    (let [data (if (.-_lastLineData this) ;;get the property
                 (str (.-_lastLineData this) chunk)
                 (str chunk))
          lines (clojure.string/split data (js/RegExp. eol "g"))]
      (set! (.-_lastLineData this) (last lines)) ;;set the property
      (doseq [line (butlast lines)]
        (.push this line))
      (done))))
{% endhighlight %}

Using `.-property` allows the compiler to rename the property during advanced compilation, e.g. `_lastLineData` could be renamed `a`. `(.-property object)` is the idiomatic way to access js properties.

## Use `(aget object "property")` for js

{% highlight clojure %}
(aget obj "property") ;;get property
(aset obj "property" v) ;;set property
 
{% endhighlight %}

If you want your code to be accessible in js, use `aget`/`aset` with strings referring to the property.

{% highlight clojure %}
(aset user "name" "boston")

;;js, works as `name` won't be renamed by the compiler
;;user.name == "boston"; ;;=> true

(set! (.-name new-user) "boston 2.0")

;;js, doesn't work as `name` could be renamed by the compiler
;;new-user.name == "boston 2.0"; ;;=> false
 
{% endhighlight %}

## Don't mix accessors

If you sometimes use `(.-property object)` and other times use `(aget object "property")`, you'll run into confusing bugs when it comes time to ship. There are few things as frustrating as bugs that only show up in prod (where you're using advanced compilation). Be consistent, and you'll save yourself unnecessary headaches.

[reference-post]: http://squirrel.pl/blog/2013/03/28/two-ways-to-access-properties-in-clojurescript/
[line-reader]: http://clojurescriptmadeeasy.com/blog/cljs-read-files-line-by-line-on-nodejs.html
[goog object]: https://google.github.io/closure-library/api/goog.object.html
[cljs api]: https://cljs.github.io/api/cljs.core/aget
[cljs-2148]: https://dev.clojure.org/jira/browse/CLJS-2148
[cljs-2149]: https://dev.clojure.org/jira/browse/CLJS-2149
