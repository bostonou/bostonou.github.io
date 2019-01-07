---
title: Don't forget about Google Closure
tags: language-learning
---

If you've spent much time with cljs, you probably know that cljs uses the Closure Compiler for code compilation. The Closure Compiler provides significant features such as dead code removal and minimization. (For more details, check out [Luke VanderHart's][luke-twitter] old but [relevant post][luke-post]. Also make sure to check out [Maria Neise's][mayaneise-twitter] awesome blog about her [cljs compiler work][mayaneise-blog] through Google's Summer of Code).

One other well-known, though often forgotten, benefit from Google Closure is the Closure Library. The Closure Library provides a huge set of functionality for free.

By all accounts, the code is high quality and covers lots of edge cases that would be difficult and painful to find. For example, what's the regular expression to determine if a string is all whitespace? If, like me, you said `^[\s]*$`... Well, thanks for playing, because that's not 100% correct. Here's what you want:

{% highlight javascript%}
//http://google.github.io/closure-library/api/source/closure/goog/string/string.js.src.html#l154
goog.string.isEmptyOrWhitespace = function(str) {
  // testing length == 0 first is actually slower in all browsers (about the
  // same in Opera).
  // Since IE doesn't include non-breaking-space (0xa0) in their \s character
  // class (as required by section 7.2 of the ECMAScript spec), we explicitly
  // include it in the regexp to enforce consistent cross-browser behavior.
  return /^[\s\xa0]*$/.test(str);
};
{% endhighlight %}

Good luck figuring out that IE's handling of `0xa0` is broken before you get to production. And you get a nice little performance optimization thrown in for free.

The Closure Library is large, and I've only used a small portion of it. As I've suggested before, we could find worse ways to spend time than reading through the [Closure api][closure-api] to get a feel for what's available.

Bonus: [Derek Slager][derek-twitter] wrote a [great post about Google Closure][derek-post] that you should definitely read. He even includes a cljs shout-out.

[luke-twitter]: https://twitter.com/levanderhart
[luke-post]: http://lukevanderhart.com/2011/09/30/using-javascript-and-clojurescript.html
[mayaneise-twitter]: https://twitter.com/mayaneise
[mayaneise-blog]: http://mneise.github.io/index.html
[closure-api]: http://google.github.io/closure-library/api/
[derek-twitter]: https://twitter.com/derekslager
[derek-post]: http://derekslager.com/blog/posts/2015/06/five-years-of-google-closure.ashx
