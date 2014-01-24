---
layout: post
title: PHP Get File Extension
excerpt: There are numerous ways to get a file extension in PHP.
  Learn how to do it right, by using the <code>pathinfo()</code> function.
---
Over time, I've seen a wide range of scripts which for one reason or another
have had the need to grab the extension from a file name or path: e.g. "gif"
from "mypic.gif".  Oftentimes the solution tends to fall back on what the task
appears to need: string manipulation.  Though, as you'll see, this may not be
the best approach to take.

To make things a little clearer for people who can visualise code more easily
than paragraphs of text, here are a few examples of what I've seen used in the
past. The samples are mostly compacted onto a single line but if you're not
comfortable with multiple things happening in one line, feel free to expand
them into multiple steps. In all cases, `$ext` ends up being _gif_.

{% highlight php startinline %}
$filename = 'mypic.gif';

// 1. The "explode/end" approach
$ext = end(explode('.', $filename));

// 2. The "strrchr" approach
$ext = substr(strrchr($filename, '.'), 1);

// 3. The "strrpos" approach
$ext = substr($filename, strrpos($filename, '.') + 1);

// 4. The "preg_replace" approach
$ext = preg_replace('/^.*\.([^.]+)$/D', '$1', $filename);

// 5. The "never use this" approach
//   From: http://php.about.com/od/finishedphp1/qt/file_ext_PHP.htm
$exts = split("[/\\.]", $filename);
$n = count($exts)-1;
$ext = $exts[$n];
{% endhighlight %}

Forgetting approach number 5 (the "never use this" approach!), they seem fairly
reasonable ways of parsing the `$filename` string and grabbing the extension.
So much so that those approaches can be found dotted around the interweb when
people ask the question "[How to get the file extension in PHP?][google]"

## Speed, baby, speed!

Because these are commonly used snippets of code, I decided to pitch them
against each other and find out which was the most efficient--by that I simply
mean the fastest processing--method just to satisfy my curiosity.  The exact
details of how they were tested I don't plan to go into in this post, but here
is what I found. On order of fastest to slowest were:

* **strrchr**
* closely followed by **strrpos**
* then **preg_replace**
* with **explode/end** coming in almost twice as slow as the str* methods

Great, so the idea is to use the `$ext = substr(strrchr($filename, '.'), 1);`
approach from now on?  Hold on there sonny boy, not so fast! There is another
contender which has not been considered yet (though some of you would have been
screaming his name from the start). Time to introduce the `pathinfo()` function.

## "Hello", says pathinfo.

`pathinfo()` does a number of things, depending on what we ask of it, but put
simply it returns (path) information about a filepath.  Full details can be
found on the handy dandy [pathinfo page][pathinfo] in the PHP manual.  Explore
the PHP manual page when you get time, but for the purpose of this blog post
I'll hone in to one specific use of the function: getting the file extension.
In order to do that, we simply call the function passing along the full
filename and a 'flag' (a constant which dictates the behaviour of the function)
asking for the extension only.

{% highlight php startinline %}
$filename = 'mypic.gif';
$ext = pathinfo($filename, PATHINFO_EXTENSION);
{% endhighlight %}

That's all there is to it!  In my opinion, this approach 'reads' much more
easily than the mess of nested function calls and playing around with string
positions, regular expressions, etc..  It is concise and to the point:
call pathinfo and only give me back the extension. Simple.

Now for the icing on the cake.  When pitched against the other methods detailed
above, this call to pathinfo beats all of the others into submission.  At least
in my testing, it is the fastest method of all (though in random hiccups strrchr
does win in around 1% of tests) being on average 1/10<sup>th</sup> faster than
even the strrchr approach. 

## Summary, or "the bit lazy people should read"

So, to cut a long blog post short, the method that I'll be using to grab
the file extension is simply:

{% highlight php startinline %}
$filename = 'mypic.gif';
$ext = pathinfo($filename, PATHINFO_EXTENSION);
{% endhighlight %}

What do you currently use? Have you had any problems using pathinfo,
any particular quirks or annoyances?

 [google]: http://www.google.com/search?q=how+to+get+the+file+extension+in+PHP%3F
 [pathinfo]: http://php.net/pathinfo
