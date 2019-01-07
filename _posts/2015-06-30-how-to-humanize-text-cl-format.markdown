---
title: How to humanize text - cl-format
tags: language-learning cljs-value how-to
---

In the cljs slack channel (which [you should join][slack]), someone asked if there is a cljs library for humanizing text. I pointed them to a new (to cljs) feature that's already available via `cljs.pprint`: `cl-format`. (Note that this also applies to clj as it already supported pretty-printing thanks to [Tom Faulhaber][tom-twitter] et al).

`cl-format` is a cljs version of Common Lisp's `FORMAT` function. It provides a ton of functionality, so much so that there's a [full pdf][pp-pdf] describing it. For a gentler, yet still expansive description, check out a [few FORMAT recipes][gigamonkeys].

Here are a few examples of humanizing text, taken from [django.contrib.humanize][django] and [HubSpot/humanize][hubspot].

From django:

* `apnumber` - For numbers 1-9, returns the number spelled out. Otherwise, returns the number. This follows Associated Press style.

* `intcomma` - Converts an integer to a string containing commas every three digits.

* `intword` - Converts a large integer to a friendly text representation.

{% highlight clojure %}
cljs.user=> (require '[cljs.pprint :refer [cl-format]])
nil
cljs.user=> (defn ap-number [n]
              ;;if (< 0 n 10) use the ~R directive, otherwise use ~A
              (cl-format nil "~:[~a~;~r~]" (< 0 n 10) n))
#<function ...>
cljs.user=> (ap-number -1)
"-1"
cljs.user=> (ap-number 0)
"0"
cljs.user=> (ap-number 1)
"one"
cljs.user=> (ap-number 9)
"nine"
cljs.user=> (ap-number 10)
"10"

cljs.user=> (defn int-comma [n] (cl-format nil "~:d" n))
#<function ...>
cljs.user=> (int-comma 1000)
"1,000"
cljs.user=> (int-comma 1000000)
"1,000,000"
cljs.user=> (int-comma 123456789)
"123,456,789"

cljs.user=> (defn int-word [n] (cl-format nil "~r" n))
#<function ...>
cljs.user=> (int-word 100)
"one hundred"
cljs.user=> (int-word 10000)
"ten thousand"
cljs.user=> (int-word 100000)
"one hundred thousand"
cljs.user=> (int-word 123456789)
"one hundred twenty-three million, four hundred fifty-six thousand, seven hundred eighty-nine"
{% endhighlight %}

From Hubspot:

* `capitalize` - Capitalizes the first letter in a string, downcasing the tail.

* `capitalizeAll` - Capitalize the first letter of every word in a string.

{% highlight clojure %}
cljs.user=> (defn capitalize [s] (cl-format nil "~@(~a~)" s))
#<function ...>
cljs.user=> (capitalize "some boring string")
"Some boring string"
cljs.user=> (capitalize "WeiRd cAsINg")
"Weird casing"

cljs.user=> (defn capitalize-all [s] (cl-format nil "~:(~a~)" s))
#<function ...>
cljs.user=> (capitalize-all "some boring string")
"Some Boring String"
cljs.user=> (capitalize-all "WeiRd cAsINg")
"Weird Casing"
{% endhighlight %}

`cl-format` also provides support for pluralization, conditional formatting, and a whole host of other things. The pluralization only supports simple cases of adding "s" and "y" => "ies" and all printing is based on English, so it's not useful in every case. But it's very nice to have and it's only a simple `(require 'cljs.pprint)` away.

[slack]: http://clojurians.net/
[gigamonkeys]: http://www.gigamonkeys.com/book/a-few-format-recipes.html
[pp-pdf]: ftp://publications.ai.mit.edu/ai-publications/pdf/AIM-1102a.pdf
[django]: https://docs.djangoproject.com/en/1.8/ref/contrib/humanize
[hubspot]: https://github.com/HubSpot/humanize
[tom-twitter]: https://twitter.com/tomfaulhaber
