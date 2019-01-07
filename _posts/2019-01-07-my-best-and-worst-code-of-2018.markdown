---
title: My Worst and Best Code of 2018
---

## The Worst

Behold, my worst code of 2018, which is actually running in production:

{% highlight clojure %}
;;this snippet of Clojure runs as part of generating the
;;server's HTML response
[:script {:type "text/javascript"}
  (str "sessionStorage.setItem('result-ids', JSON.stringify(["
    (apply str (interpose ", " (map :id results)))
    "]));")]
{% endhighlight %}

What's this do? It's Clojure running on the server, which generates JavaScript that will run on the browser.

1. Collect the `:id`'s of the result set
2. Place a `, ` (comma with trailing space) string between each value of the result set
3. Concatenate the values and interposed strings
4. Concatenate that into a string of JavaScript which will run when the browser loads the script
5. The JavaScript converts what is now an array of the `:id`'s into a string
6. Store the stringified array into the SessionStorage

### What's Bad About It?

1. A hackish combination of Clojure and JavaScript
2. Arbitrarily stuck in the DOM, with no consideration of when/where it will run
3. No consideration of `SessionStorage` being available or space availability
4. Whatever code uses the value in `SessionStorage` needs to know the specific key `result-ids`
5. Who's responsible for cleaning this up (hint: no one, it just stays around for the full session)
6. It's way too hard to test

This is bad code and I should feel bad. It's a maintenance nightmare and only had a few minutes worth of design time.

## The Best

As you might guess, my best code of 2018 happens to be that same code. Why?

I was going over a recent release of a few features with the head of a one-person department. He was pleased with the work we'd done, but as we closed up the meeting he mentioned something that was a hassle but probably not a big deal. For him to work through his process, he regularly had to click then scroll then click. It added some extra time, but mostly it was annoying and interrupted his flow. He said not to spend much time on it but just wanted to mention it.

So I gave it a few minutes thought and came up with the approach above. I wrote it, briefly tested it, then deployed it to production. The client was happy with a quick fix and that was that.

### Why It's The Best

The one-person department I was working with was the fastest growing part of the company, and the company was turning up the marketing dollars to test exactly how much business they could gain *and* handle. The CEO was trying to drown this person in business, and it was my job to keep him afloat.

This was my favorite and best code of the year because it was so antithetical to what I and other devs often treat as Good Code™. It's not well thought out, it's not maintainable or testable, it's not ClojureScript. But a few minutes worth of work improved the efficiency and lowered the annoyance of one person doing millions of dollars in business. I'd have a hard time finding any other code I wrote that provided that kind of return on investment.

**My best code did all the things my client cared about and almost none of the things I cared about.** 
