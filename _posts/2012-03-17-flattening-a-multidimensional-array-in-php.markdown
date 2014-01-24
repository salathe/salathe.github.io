---
layout: post
title: Flattening a multidimensional array in PHP
excerpt: The task is to "flatten" nested arrays into a single series containing
  all of the values.
---
This post is going to serve as a place to point people to, as it is a question
that gets asked fairly regularly in the PHP support channels that I hang
around in.  The task is to "flatten" nested arrays into a single series
containing all of the values.

## Example input array

{% highlight php startinline %}
$input = array('a', 'b', array('c', 'd'), 'e', array('f'), 'g');
{% endhighlight %}

## Expected output array

{% highlight php startinline %}
$output = array('a', 'b', 'c', 'd', 'e', 'f', 'g');
{% endhighlight %}

It looks like most people usually resort to a function that calls itself
recursively to build the output array.  My two examples below will give the
expected output array, but will let PHP take care of the recursion for us.

## Iterator, Iterator, Iterator, Iterator

This is my preferred way. OK, sure it creates a hierarchy of objects only to
mush the values into an array, but it's nice and concise. Besides, anyone who
knows me knows I'm a fan of the [SPL](http://php.net/spl "Standard PHP Library").

{% highlight php startinline %}
$output = iterator_to_array(new RecursiveIteratorIterator(
    new RecursiveArrayIterator($input)), FALSE);
{% endhighlight %}

## Walking the array recursively

This is a more procedural approach, but again it saves us from thinking about
the recursion. It uses a [closure](http://php.net/closures) to build up the
array step by step.

{% highlight php startinline %}
$output = array();
array_walk_recursive($input, function ($current) use (&$output) {
    $output[] = $current;
});
{% endhighlight %}

## Resources

* [RecursiveArrayIterator](http://php.net/recursivearrayiterator "RecursiveArrayIterator")
* [RecursiveIteratorIterator](http://php.net/recursiveiteratoriterator "RecursiveIteratorIterator")
* [iterator\_to_array()](http://php.net/iterator_to_array "iterator_to_array()")
* [Closures](http://php.net/closures "Closures")
* [array\_walk_recursive()](http://php.net/array_walk_recursive "array_walk_recursive()")
