---
layout: post
title: Modal value(s) in PHP
excerpt: Creating a function to find the most common value(s) in an array.
---
I was browsing around the <a href="http://bryanculver.com/" rel="external friend">website</a> of someone on <a href="http://webetalk.com" rel="external">IRC</a> and found that they had written a function to determine the mode of an array of values.  <a href="http://bryanculver.com/find-mode/" rel="external friend">The function</a> seems a little convoluted so in a spare 15 minutes I decided to re-implement it, take care of some of its caveats and make a quick blog post, the one you see here.<a id="more"></a><a id="more-156"></a>

<h3>The function: array_mode</h3>
<pre lang="php">/**
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
}</pre>

<h3>Examples</h3>
Hopefully how this is used should be obvious, since it's such a simple function. If you really must have something to copy-and-paste:
<pre lang="php">$single_mode = array('a', 'b', 'a', 'b', 'c', 'a');
$multi_mode  = array(0, 0, 2, 2, 1, 1, 3);
$no_mode     = array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

var_dump(
	array_mode($single_mode), // "a"
	array_mode($multi_mode),  // array(0,2,1)
	array_mode($no_mode)      // FALSE
);</pre>

<h3>Quick discussion</h3>
The actual process is fairly simple but note that it needs PHP 5.1 or higher (because we use type hinting in the function definition) which shouldn't be an issue since PHP 5.2.9 is the current stable release and if you're still stuck with PHP 4, well I'm sorry. <ins datetime="2009-04-01T19:16:00+0100">[Update: Bryan posted up a <a href="http://bryanculver.com/find-mode/" rel="external">PHP4-compatible version</a> if you need it]</ins>

The magic is really taken care of by the first three lines inside the function which counts how often any value occurs within the supplied array, then grabs the ones which occur most often (the mode<ins datetime="2010-07-1T07:53:00+0000">; <a href="#comment-59895">thanks Rob</a></ins>).  The rest of the function just takes care of the return values given different circumstances: if the is no mode (all values occur once only), just one mode (that value is returned) or multiple modal values (an array of these values is returned).

In the case that there are multiple modal values, the returned array will have the values in the order in which they were assigned in the original array. It's trivial to sort them, if that's what you need.
