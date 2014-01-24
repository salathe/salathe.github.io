---
layout: post
title: Anonymous Functions and Closures (as of PHP 5.3)
excerpt: A brief introduction to anonymous function literals and closures.
---
There has been much discussion, especially of late considering the release of PHP 5.3, on the subject of anonymous functions and closures (both terms appear to be used interchangeably in the <a href="http://php.net/closures" title="Closures in the PHP Manual">documentation</a>).  They are not the same, though many folks are introduced to them at the same time.<a id="more"></a><a id="more-234"></a>

<h3>Anonymous Functions</h3>
Put plainly, an anonymous function is a function without a name. That's all, there's nothing magic or particularly technical about it.  There are a few ways to create an anonymous function in PHP; up until PHP 5.3 came around the usual way was to use <a href="http://php.net/create_function" title="create_function documentation on php.net"><code>create_function</code></a> (see <a href="#fig1" title="View figure 1: Anonymous function with create_function">fig. 1</a>) which takes a couple of arguments as strings and spits out a function, in PHP 5.3 we're allowed to create anonymous functions looking much more like normal function definitions (see <a href="#fig2" title="View figure 2: Anonymous function in PHP 5.3">fig. 2</a>).

<h4 id="fig1">Fig. 1: Anonymous function with create_function</h4>

{% highlight php %}
<?php
$square = create_function('$x', 'return $x*$x;');
echo $square(2); // 4
?>
{% endhighlight %}

<h4 id="fig2">Fig 2: Anonymous function as of PHP 5.3</h4>
<pre lang="PHP">
<?php
$square = function($x) {
    return $x * $x;
};
echo $square(2); // 4
?>
</pre>

I hope you'll agree that the latter is a much nicer way (visually) of defining an anonymous function, quite apart from the more logical and performance-related (see <a href="http://fabien.potencier.org/article/17/on-php-5-3-lambda-functions-and-closures#comments" title="Comments on 'On PHP 5.3, Lambda Functions, and Closures'">comment #4</a>) aspects of using it.

<h3>Closures</h3>
A closure is a (anonymous) function that is aware of its context. For all intents and purposes, think of them being anonymous functions which know about some variables which weren't defined within them. In geeky language, they are functions which close over (hence "closure") free variables, the latter being variables which are not local nor arguments to the function.  It is easier to see with an example, I think.

<h4 id="fig3">Without Closure</h4>
Lets say that we need a function to add three to a number (overly basic, I know). That's easy.
<pre lang="PHP">
<?php
$add_3 = function($x) {
    return $x + 3;
};
echo $add_3(10); // 13
?>
</pre>
Now if we wanted another function to behave similarly but instead it should add 4 to the supplied number, we could just as easily create a new anonymous function to do the job.
<pre lang="PHP">
<?php
$add_4 = function($x) {
    return $x + 4;
};
echo $add_4(10); // 14
?>
</pre>

If, however, you wanted to do that for a series of different additions then things could get very repetitive, very quickly!  If only we could dynamically create an anonymous function to do what we wanted: enter the closure.

<h4 id="fig4">With Closure</h4>
We can define an anonymous function (it needn't be anonymous but lets keep on theme) which returns anonymous function based on arguments supplied to it. Remember our definition of a closure above, specifically making use of 'free' variables, that is what our <code>$value_to_add</code> is in the example below.
<pre lang="PHP">
<?php
$add = function($value_to_add) {
    return function ($x) use ($value_to_add) {
        return $x + $value_to_add;
    };
};

$add_3 = $add(3);
$add_4 = $add(4);

echo $add_3(10); // 13
echo $add_4(10); // 14
?>
</pre>

<h3>A more practical example</h3>
As fun as silly adding examples are, there will be undoubtedly someone who has been reading everything above (good job getting this far!) and cannot see a practical use for all of this. Since <a href="http://www.letour.fr/" title="Site officiel du tour de France">le Tour de France</a> is on, lets have a play on words and have an example on cycling (over a series of values).

<h4 id="fig5">Cycling with Closures</h4>
<pre lang="PHP">
<?php
// Cycle factory: takes a series of arguments
// for the closure to cycle over.
function cycle() {
    $items = func_get_args();
    // Create closure using the supplied list of arguments
    return function() use (&$items) {
        // Get current item
        $current = each($items);
        // Rewind if at the end of the list
        if ($current === FALSE) {
            reset($items);
            $current = each($items);
        }
        // Return the current item
        return $current['value'];
    };
}

// Create our list of CSS classes to cycle over
$class = cycle('odd', 'even');
// List of adjectives, why not
$taste = cycle('vile', 'yummy', 'delicious');

// Our list of items to be formatted into a pretty HTML list
$items = array('apple', 'banana', 'cherry', 'date', 'elderberry', 'figs');

?>
<ul>
<?php foreach ($items as $item) : ?>
	<li class="<?php echo $class() ?>"><?php echo $item ?> are <?php echo $taste() ?></li>
<?php endforeach ?>
</ul>

</pre>

That example would provide the following output:
<pre lang="html4strict">
<ul>
	<li class="odd">apples are vile</li>
	<li class="even">bananas are yummy</li>
	<li class="odd">cherries are delicious</li>
	<li class="even">dates are vile</li>
	<li class="odd">elderberries are yummy</li>
	<li class="even">figs are delicious</li>
</ul>
</pre>

Well, it's a little bit more practical anyway!  Have you been playing with closures and/or anonymous functions too?
