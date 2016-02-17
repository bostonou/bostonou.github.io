---
title: Checking out CLJSJS Packages
---

If you've ever wanted to include a JS lib in your cljs projects, you know there are several steps involved:

* Setup your development and production (minified) versions
* Write your externs file
* Include it as a foreign lib or package it up as a jar

[Those steps][foreign-libs] aren't too big a deal (though getting your externs correct can be confusing), but it'd be nice if we weren't all repeating this process on our own.

### Enter CLJSJS Packages

Several members of the cljs community got together and decided to put together a [collection of commonly used JS packages][cljsjs-packages] that are trivial to use in cljs.

For example, using React goes from something like this:

{% highlight clojure %}
;;...details elided
{:compiler {:foreign-libs [{:file "resources/public/js/react.inc.js"
                            :file-min "resources/public/js/react.min.inc.js"
                            :provides ["cljsjs.react"]}]
            :externs ["externs/react.ext.js"]}}
{% endhighlight %}

to this:

{% highlight clojure %}
[cljsjs/react "0.14.3-0"]
{% endhighlight %}

Now we can add JS libs just like all of our other dependencies. And as a bonus, all of our externs are handled for us. Yey!

There are quite a few JS libs packaged up already, so there's a good chance whatever lib you want is already there. And if not, packaging and contributing your own is a pretty soft entry into giving back to the cljs community. [The instructions for creating a package][create-instructions] are really good and straightforward.

### Bonus

If you still struggle with externs or just want a better understanding, go look through some of the externs in various packages. The [externs write up][externs-write-up] is also worth a read.

[foreign-libs]: https://github.com/clojure/clojurescript/wiki/Packaging-Foreign-Dependencies
[cljsjs-packages]: https://github.com/cljsjs/packages
[create-instructions]: https://github.com/cljsjs/packages/wiki/Creating-Packages
[externs-write-up]: https://github.com/cljsjs/packages/wiki/Creating-Externs
