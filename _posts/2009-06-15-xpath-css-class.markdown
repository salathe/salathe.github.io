---
layout: post
title: XPath for CSS classes
excerpt: How to match a specific CSS class name in an XPath query.
---
I was playing around with <a href="http://developer.yahoo.com/yql/" title="Yahoo! Query Language">YQL</a> today and was scraping a HTML document (no details, that's not the point of this blog entry).  Part of the processes meant that I needed to check for a specific CSS class attached to some elements. 

Easy enough, XPath contains (pardon the pun, see later) some nifty functions which can act on nodes and their values to be used in conditional checks like I needed. I've been using the technique outlined in this post for a very long while (usually with XPath in PHP) and originally stumbled upon the basic idea from <a href="http://plasmasturm.org/log/444/">How to map CSS selectors to XPath queries</a> (Thanks ever so much!) Today's use of it prompted this blog post. <a id="more"></a><a id="more-203"></a>

For example, to grab any <code>li</code> elements with a class of <var>active</var> (and only that one class) out of the document it would be easy enough to use:
<pre lang="xpath">
//li[@class="active"]
</pre>

Now, as I said that will only grab the list items which have that specific class like <code>&lt;li class="active"&gt;</code>.  What if there is more than one class in there, ala <code>&lt;li class="hot active"&gt;</code>? We could use the XPath function <code>contains</code> which looks to see if a string contains another string but that would cause problems (e.g. searching for "a" in conjunction with classes like "active", "nav", etc. would result in false positives).

To match any list item with a classes which contains the letter "a" can be sought with:
<pre lang="xpath">
//li[contains(@class, "a")]
</pre>

Nearly there, now to work out how to not match false positives.  To do this, we can look for "<code>[space]a[space]</code>" with a slightly modified class string (classes at the  start/end of the string wouldn't normally have spaces before/after respectively). Whilst we're at it, we can <em>normalize</em> the spaces in the class string which converts any number of spaces, tabs, etc. characters into just a single space.

So, to properly get at a class name of "<var>active</var>" for all list items, regardless of its position in the class string, the following XPath query can be used.
<pre lang="xpath">
//li[contains(concat(" ", normalize-space(@class), " "), " active ")]
</pre>
<span class="aside">Note: there are single spaces in the first and last arguments for <code>concat</code>, and on either side of the word <code>active</code></span>.

Just as an observation, the comparable CSS selector to do this would be <code>li[class~="active"]</code> (or the more usual <code>li.active</code>) which would be awesome to use if XPath decided to implement a similar syntax.
