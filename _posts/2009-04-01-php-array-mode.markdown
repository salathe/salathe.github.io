---
layout: post
title: Modal value(s) in PHP
excerpt: Creating a function to find the most common value(s) in an array.
---
I was browsing around the [website][bryan] of someone on [IRC][irc] and found
that they had written a function to determine the mode of an array of values.
[The function][bryan-article] seems a little convoluted so in a spare
15 minutes I decided to re-implement it, take care of some of its caveats
and make a quick blog post, the one you see here.

## The function: array_mode

{% highlight php startinline %}
/**
 * Returns the mode value(s) for an array.
 *
 * @param array The array of values to determine the mode.
 * @return mixed Return mode value if there is only one, array of values if bimodel or FALSE if no mode.
 */
function array_mode(array $set)
{
	$counts = array_count_values($set);
	arsort($counts); // Sort counts in descending order
	$modes  = array_keys($counts, current($counts), TRUE);

	// If each value only occurs once, there is no mode
	if (count($set) === count($counts))
		return FALSE;
	
	// Only one modal value
	if (count($modes) === 1)
		return $modes[0];
	
	// Multiple modal values
	return $modes;
}
{% endhighlight %}

## Examples

Hopefully how this is used should be obvious, since it's such a simple function.
If you really must have something to copy-and-paste:

{% highlight php startinline %}
$single_mode = array('a', 'b', 'a', 'b', 'c', 'a');
$multi_mode  = array(0, 0, 2, 2, 1, 1, 3);
$no_mode     = array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

var_dump(
	array_mode($single_mode), // "a"
	array_mode($multi_mode),  // array(0,2,1)
	array_mode($no_mode)      // FALSE
);
{% endhighlight %}

## Quick discussion

The actual process is fairly simple but note that it needs PHP 5.1 or higher
(because we use type hinting in the function definition) which shouldn't be an
issue since PHP 5.2.9 is the current stable release and if you're still stuck
with PHP 4, well I'm sorry. <ins datetime="2009-04-01T19:16:00+0100">[Update:
Bryan posted up a [PHP4-compatible version][bryan-article] if you need it]</ins>

The magic is really taken care of by the first three lines inside the function
which counts how often any value occurs within the supplied array, then grabs
the ones which occur most often (the mode).  The rest of the function just takes
care of the return values given different circumstances: if the is no mode
(all values occur once only), just one mode (that value is returned) or
multiple modal values (an array of these values is returned).

In the case that there are multiple modal values, the returned array will have
the values in the order in which they were assigned in the original array.
It's trivial to sort them, if that's what you need.

 [bryan]: http://bryanculver.com/
 [irc]: http://webetalk.com
 [bryan-article]: http://bryanculver.com/find-mode/
