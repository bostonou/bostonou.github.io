---
title: JS interop - property access
---

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
;;new-user.name == "boston 2.0"; ;;false
{% endhighlight %}

## Don't mix accessors

If you sometimes use `(.-property object)` and other times use `(aget object "property")`, you'll run into confusing bugs when it comes time to ship. There are few things as frustrating as bugs that only show up in prod (where you're using advanced compilation). Be consistent, and you'll save yourself unnecessary headaches.

[reference-post]: http://squirrel.pl/blog/2013/03/28/two-ways-to-access-properties-in-clojurescript/
[line-reader]: http://clojurescriptmadeeasy.com/blog/cljs-read-files-line-by-line-on-nodejs.html