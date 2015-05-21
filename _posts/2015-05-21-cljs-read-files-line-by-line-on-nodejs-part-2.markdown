---
title: CLJS - Read files line by line on NodeJS Part 2
---

> Note: There are a number of things that may be unfamiliar to new cljs devs. Rather than making this a huge post, I'll write a separate post for each concept. For example, if the `lazy-cat` used in this post is unclear, check out [my explanation of lazy-seq and recursion][laziness-post].

In my [last post][line-by-line-post], we had some code that could read files line by line. We then used each line, either by a callback or a channel. While the code *worked*, I didn't want the asynchronous reads. Instead, I wanted to get as close to this as possible:

{% highlight clojure %}
(with-open [rdr (open-file path)]
  (doseq [line (line-seq rdr)]
    (js/console.log line))) ;;do something with line
{% endhighlight %}

This has the following benefits:

1. It's exactly the same as clj
2. I don't have to think about async code that provides no benefits here
3. It lazily reads in the file and handles cleanup
4. It's a sequence and I can program to that interface

So the first thing was to switch from [`createReadStream`][readStream] to [`readSync`][readSync]. [`readSync`][readSync] synchronously reads in a user specified number of bytes.

{% highlight clojure %}
(def fs (js/require "fs")) ;;require nodejs lib

(defn- read-chunk [fd]
  (let [length 128
        b (js/Buffer. length) ;;Buffer is a global nodejs lib
        bytes-read (.readSync fs fd b 0 length nil)]
    (if (> bytes-read 0)
      (.toString b "utf8" 0 bytes-read))))
{% endhighlight %}

We can now read an arbitrary amount of data from the file, but we have to manage finding actual lines of text. Here's the logic we need to implement:

1. Get the next chunk of data
2. If it contains a line of text, return that line of text and save the remaining text for the next call
3. If it does not contain a line of text, get more data and try our line check again
4. If we read the EOL, just return whatever data we have

Here's what I ended up with:

{% highlight clojure %}
(defn line-seq
  ([fd]
    (line-seq fd nil))
  ([fd line]
   (if-let [chunk (read-chunk fd)]
     (if (re-find #"\n" (str line chunk))
       (let [lines (clojure.string/split (str line chunk) #"\n")]
         (if (= 1 (count lines))
           (lazy-cat lines (line-seq fd))
           (lazy-cat (butlast lines) (line-seq fd (last lines)))))
       (recur fd (str line chunk)))
     (if line
       (list line)
       ()))))
{% endhighlight %}

`line-seq` takes a file descriptor and lazily reads from the file, returning a lazy sequence of lines.

The last bit is our `with-open` macro. *Note: cljs macros have to be written in clojure*

{% highlight clojure %}
(defmacro with-open [bindings & body]
  (assert (= 2 (count bindings)) "Incorrect with-open bindings")
  `(let ~bindings
     (try
       (do ~@body)
       (finally
         (.closeSync cljs-made-easy.line-seq/fs ~(bindings 0))))))
{% endhighlight %}

Now, we're where we want to be:

{% highlight clojure %}
(with-open [fd (.openSync fs "cljs-love.txt")]
  (doseq [line (line-seq rdr)]
    (js/console.log line))) ;;do something with line
{% endhighlight %}

And since we have a `seq`, we can use all of our normal tools:

{% highlight clojure %}
(with-open [fd (.openSync fs "cljs-love.txt")]
  ;;only return lines that contain "awesome"
  (doseq [line (filter #(re-find #"awesome" %) (line-seq rdr))]
    (js/console.log line))) ;;nothing but awesome
{% endhighlight %}

Boom. Pretty sweet.

Here's a [gist of the full code][gist].

[laziness-post]: http://clojurescriptmadeeasy.com/blog/how-to-follow-recursion-with-lazy-sequences.html
[line-by-line-post]: http://clojurescriptmadeeasy.com/blog/cljs-read-files-line-by-line-on-nodejs.html
[readStream]: https://nodejs.org/api/fs.html#fs_fs_createreadstream_path_options
[readSync]: https://nodejs.org/api/fs.html#fs_fs_readsync_fd_buffer_offset_length_position
[gist]: https://gist.github.com/bostonou/a54c029fa6f29459eafe
