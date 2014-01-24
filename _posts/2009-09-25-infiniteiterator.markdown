---
layout: post
title: The InfiniteIterator in PHP
excerpt: 'Introducing the <a href="http://php.net/InfiniteIterator" title="PHP
  Manual: InfiniteIterator">InfiniteIterator</a> which is part of the
  <a href="http://php.net/spl" title="Standard PHP Library">Standard PHP Library</a>
  (SPL).'
---
In an article back in July ([Anonymous Functions and Closures (as of PHP 5.3)](http://cowburn.info/2009/07/08/anonymous-closures/ "Read Anonymous Functions and Closures (as of PHP 5.3)")), I gave [an example](http://cowburn.info/2009/07/08/anonymous-closures/#fig5 "See example: Cycling with Closures") of looping over a series of values repeatedly.  Whilst that example does the job (and introduces the concept of closures) it's hardly the most convenient method of repeatedly iterating over a series of values.  Introducing the [`InfiniteIterator`](http://php.net/InfiniteIterator "PHP Manual: InfiniteIterator") which is part of the [Standard PHP Library](http://php.net/spl "Standard PHP Library") (SPL).

On the plus side, for those who have yet to take the plunge into using PHP 5.3, this iterator has been part of the core of PHP as of 5.1.0 so there is a much greater chance of actually being able to use it right now.  Bear in mind that the SPL is supposed to work together with its component parts so if [iterators](http://php.net/iterator "PHP: Object Iteration") are a foreign concept, some of this post might be a little unclear but it is not the purpose of this post to outline iterators in PHP.

As eluded to above, the `InfiniteIterator` comes in useful when you have an existing `Iterator` (anything that implements that interface; a directory listing (`DirectoryIterator`), a file (`SplFileObject`), an array (with `ArrayIterator`), and so on) and wish to iterate over its contents again and again. 

### An `InfiniteIterator` Example

Here is a basic example that I wrote for the [documentation](http://php.net/InfiniteIterator.construct "PHP Manual: InfiniteIterator::__construct"). Note that a [`LimitIterator`](http://php.net/LimitIterator "The LimitIterator class") is used to restrict the values which are iterated over (otherwise the loop would go on forever!).

```php
<?php
$arrayit  = new ArrayIterator(array('cat','dog'));
$infinite = new InfiniteIterator($arrayit);
$limit    = new LimitIterator($infinite, 0, 7);
foreach($limit as $value)
{
    echo "$value\n";
}
?>
```

This example outputs something along the lines of:

```
cat
dog
cat
dog
cat
dog
cat
```

Can you think of any more useful instances where the `InfiniteIterator` might come in useful?
