---
layout: post
title: Parsing Dates with DateTime
excerpt: What to do when <code>strtotime()</code> misbehaves.
---
This post comes on the back of a number of forum posts I've seen floating around
where the Original Poster (OP) asks a question along the lines of,
"<q>I have a date in dd/mm/yyy format, how can I work with it?</q>"

The problem is that the most common way of parsing strings into a more usable
format (Unix timestamp) is to use `strtotime()`. However, this function does
not like <samp>dd/mm/yyyy</samp> formatted strings and will at best parse the
string as <samp>mm/dd/yyyy</samp> and at worst return <var>FALSE</var>
(if it could not parse the date string as mm/dd/yyyy).

## Quick workaround

One quick thing that works, and I've advocated in forum posts before, is to
provide a date string in the format <samp>dd-mm-yyyy</samp>. Note that the
date parts are in the same order, just the separator has changed from a forward
slash (`/`) to a hyphen (`-`).  A date formatted in this way will be happily
parsed by `strtotime()`. For example:

{% highlight php startinline %}
date_default_timezone_set('UTC');
$date = '14/06/2009';                 // dd/mm/yyyy
$date = str_replace('/', '-', $date); // dd-mm-yyyy
echo date('r', strtotime($date));
// Sun, 14 Jun 2009 00:00:00 +0000
{% endhighlight %}

## Using DateTime

As of PHP 5.2.0 (and experimentally in 5.1.x) we have also had the ability to
play with dates and times using the built-in `DateTime` extension (Aside: the
`date_default_timezone_set` function used above comes from this extension).
To repeat the above but in `DateTime` form:

{% highlight php startinline %}
$date = '14/06/2009';                 // dd/mm/yyyy
$date = str_replace('/', '-', $date); // dd-mm-yyyy
$date = new DateTime($date, new DateTimeZone('UTC'));
echo $date->format('r');
// Sun, 14 Jun 2009 00:00:00 +0000
{% endhighlight %}

## Help Coming!

I'm sure that you'll agree, the above is hardly a perfect solution to the
problem (but is a sufficient workaround for now).  However help is at hand.
As of **PHP 5.3.0** we will be able to make use of a new static method on the
`DateTime` class.  This method is `DateTime::createFromFormat()` which requires
two arguments; a string specifying the format which the date string has (as used
by the `date()` function), and the date string itself (there is also a third,
optional, argument for the timezone to be used).

An example of its use, analogous to the snippets above, would be:

{% highlight php startinline %}
$date = DateTime::createFromFormat('d/m/Y', '14/06/2009', new DateTimeZone('UTC'));
$date->setTime(0,0,0);
echo $date->format('r');
// Sun, 14 Jun 2009 00:00:00 +0000
{% endhighlight %}

<small>
Aside: For my installation (PHP 5.3.0RC3) this method does not parse the
date string as midnight, as the other code snippets do, but will use current
time! This is why the `setTime()` method (available as of 5.2.0) is used, to
force the time to be midnight (arguments are <var>hour</var>, <var>minute</var>,
<var>second</var>). _Edit:_ One could instead use the `!` within the format
string like `!d/m/Y`, since the time portion is missing it will substitute the
Unix epoch's time (midnight) in its place. See the manual for details of `!`.
</small>

Just to wrap things up, the `DateTime::createFromFormat()` should help
considerably towards parsing previously ambiguous date strings into forms that
can be manipulated and at least will get around the string manipulation being
offered as a solution right now. If you're still stuck playing around with the
date string, at least  you know that <samp>dd-mm-yyyy</samp> will be parsed
as you want.
