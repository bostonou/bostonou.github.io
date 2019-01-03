---
title: JS Changes Fast; CLJS Makes It Manageable
---

I wrote my first JavaScript somewhere around 15 years ago and switched almost exclusively to ClojureScript several years ago. JavaScript as a language has changed so much that, if you drop me in any modern js codebase, it's probably 50/50 that I could actually read it without referencing google.

JavaScript is constantly changing with new syntax, new data structures, new features, etc. And for the most part, using ClojureScript just lets me ignore the constant change and focus on solving problems.

## Changes in JavaScript, Handled by ClojureScript

#### Pipeline operator

When the pipeline operator was first proposed, developers seemed very excited about it. I was confused at the excitement because it's just the [threader macro][threader] in ClojureScript.

<sub>Example from [*How to try the JavaScript Pipeline Operator Today*](https://itnext.io/how-to-try-the-javascript-pipeline-operator-today-e3f75eb12cf1)</sub>

{% highlight javascript %}
//js
const double = (n) => n * 2;
const increment = (n) => n + 1;

// without pipeline operator
double(increment(5)); // 12

// with pipeline operator
const result = 5 
  |> increment 
  |> double
result // 12
{% endhighlight %}

{% highlight clojure %}
;;cljs
(defn double [n]
  (* n 2))
(defn increment [n]
  (+ n 1))
  
;;without threader
(double (increment 5)) ;; 12

;;with threader
(-> 5
  increment
  double)

;; also have ->>, as->, some->, and cond->
{% endhighlight %}

### Optional Chaining Operator

The optional chaining operator cleans up all checks that your properties exist when navigating an object. In ClojureScript, we would use a map and just use [`get-in`][get-in]. And if we have an object, we use [Google Closure's `goog.object`][gobj-keys].

<sub>Example from [*ESNext: JavaScript "Optional Chaining Operator"*](https://www.bram.us/2017/01/30/javascript-null-propagation-operator/)</sub>

{% highlight javascript %}
//js
const message = {
  body: {
    user: {
      firstName: 'Bramus',
      lastName: 'Van Damme',
    },
  },
};

//have to make sure all properties exist
const firstName = (message.body && message.body.user &&
                   message.body.user.firstName) || 'Stranger';

//don't need all the intermediate checks
const firstName = message.body?.user?.firstName || 'Stranger';
{% endhighlight %}

{% highlight clojure %}
;;cljs with a map
(def message {:body {:user {:first-name "Bramus" :last-name "Van Damme"}}})

(let [first-name (get-in message [:body :user :first-name] "Stranger")]
  ...)
{% endhighlight %}

{% highlight clojure %}
;;cljs with an object
(def message #js {:body #js {:user #js {:first-name "Bramus"
                                        :last-name "Van Damme"}}})

(let [first-name (or (goog.object/getValueByKeys
                       message
                       #js ["body" "user" "first-name"])
                     "Stranger")]
  ...)
{% endhighlight %}

#### Object Manipulation

There are plenty of new ways of defining, destructuring, and working with objects. Most of these are irrelevant to ClojureScript because we use maps instead of objects and have a powerful API to work with data. The new [`Object.entries`][obj-entries] and [`Object.values`][obj-values] are just [`keys`][clj-keys] and [`vals`][clj-vals] when working with maps. [JavaScript added destructuring and default values][destruct] which are just part of ClojureScript's core language.

#### Generators and Async/Await

I can see why JavaScript developers are excited about Generators and Async/Await, but I've yet to see anything that couldn't be implemented with [core.async][core-async]. It wouldn't surprise me if ClojureScript could use these internally for performance gains, but as a ClojureScript user it's not a new feature that unlocks new value.

#### Regular Expressions

Regular expressions are one exception where ClojureScript developers need to keep up with JavaScript, because [ClojureScript regular expressions][cljs-regex] compile to native JavaScript regular expressions. There are [several][regex-1] [proposals][regex-2] for [new][regex-3] [features][regex-4] with JavaScript regular expressions that will be visible in ClojureScript.

---
<br />
In general, ClojureScript makes it so that I just don't think much about JavaScript as a language. ClojureScript deserves a lot of praise for the stability it provides given the frequent changes of the language it's built on. For the last several years, ClojureScript developers have been free to spend time learning a consistent API without needing to track frequent language and syntax changes.

[threader]: https://clojure.org/guides/threading_macros
[get-in]: https://clojuredocs.org/clojure.core/get-in
[gobj-keys]: https://google.github.io/closure-library/api/goog.object.html
[obj-entries]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries
[obj-values]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Object/values
[clj-keys]: https://clojuredocs.org/clojure.core/keys
[clj-vals]: https://clojuredocs.org/clojure.core/vals
[destruct]: http://es6-features.org/#ObjectAndArrayMatchingDefaultValues
[cljs-regex]: https://cljs.github.io/api/syntax/regex
[regex-1]: https://github.com/tc39/proposal-regexp-dotall-flag
[regex-2]: https://github.com/tc39/proposal-regexp-named-groups
[regex-3]: https://github.com/tc39/proposal-regexp-lookbehind
[regex-4]: https://github.com/tc39/proposal-regexp-unicode-property-escapes
[core-async]: https://github.com/clojure/core.async
