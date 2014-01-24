---
layout: post
title: Using PHP Functions in XPath Expressions
excerpt: The <code>DOMXPath::registerPHPFunctions()</code> method is available as
  of PHP 5.3.0 and allows the use of PHP functions (and static methods)
  within XPath queries to complement the normal set of XPath functions.
---
Disclaimer: this article expects familiarity with using the DOM<sup>[1][fn1]</sup>
extension and XPath<sup>[2][fn2]</sup> expressions. 

The (<del datetime="2009-10-26T14:15:44Z">currently undocumented</del>
<ins datetime="2009-10-26T14:15:44Z">now documented</ins><sup>[3][fn3]</sup>)
`DOMXPath::registerPHPFunctions()` method is available as of PHP 5.3.0 (it was
added to the code base back in December 2006) and allows the use of PHP
functions (and static methods) within XPath queries to complement the normal
set of XPath functions<sup>[2][fn2]</sup>.

## Description

`void DOMXPath::registerPHPFunctions  ([ string|array <var>$restrict</var>] )`

Enables the use of PHP functions as XPath functions.

## Parameters

<dl>
    <dt>restrict</dt>
    <dd>
        Use this parameter to only allow certain functions to be called from
        XPath; it can be either a string (a function name) or an array of
        function names.
    </dd>
</dl>

## Return Values

No value is returned.

## Examples

**Note:** The following examples load a sample XML document called
<kbd>book.xml</kbd> with the following contents:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<books>
    <book>
        <title>PHP Basics</title>
        <author>Jim Smith</author>
        <author>Jane Smith</author>
    </book>
    <book>
        <title>PHP Secrets</title>
        <author>Jenny Smythe</author>
    </book>
    <book>
    	<title>XML basics</title>
    	<author>Joe Black</author>
    </book>
</books>
{% endhighlight %}

### <span id="ex1">Example #1: Call a PHP function in XPath with `php:functionString()`</span>

This example demonstrates the basic use of `DOMXPath::registerPHPFunctions()`
by replicating the `substring()` XPath function. The first thing that needs to
be done is to register the <samp>php</samp> namespace with the associated URI
<samp>http://php.net/xpath</samp>. Don't question it, it just needs to be done!

Next, we call `DOMXPath::registerPHPFunctions()` on our object. If no arguments
are used, as in this example, then the range of functions allowed to be called
is not restricted—you can call any<sup>[4][fn4]</sup> function from XPath. I
would always advise restricting the functions which can be called,
see [example 2][ex2].

Within our XPath query, we use `php:functionString()` which allows us to name
a function and provide some parameters (or indeed, no parameters) to be passed
to that function. There are two flavours which can be used here:
`php:functionString()` which passes an XML node/attribute as a string and
`php:function()` (see [example 3][ex3]) which passes an array of XML
node/attribute objects (in `DOMElement` / `DOMAttr` / etc. form) to the
function .  In this example the PHP function [`substr()`][substr] is called,
passing along the book's title (in string form), an offset of <samp>0</samp>
and length of <samp>3</samp>. This returns the first 3 characters of the book's
title which is then compared to the string <kbd>PHP</kbd> in order to filter our
list of books down to those having titles starting with <kbd>PHP</kbd>.

{% highlight php %}
<?php
$dom = new DOMDocument;
$dom->load('book.xml');

$xpath = new DOMXPath($dom);

// Register the php: namespace (required)
$xpath->registerNamespace("php", "http://php.net/xpath");

// Register PHP functions (no restrictions)
$xpath->registerPHPFunctions();

// Call substr function on the book title
$nodes = $xpath->query('//book[php:functionString("substr", title, 0, 3) = "PHP"]');

echo "Found {$nodes->length} books starting with 'PHP':\n";
foreach ($nodes as $node) {
	$title  = $node->getElementsByTagName("title")->item(0)->nodeValue;
	$author = $node->getElementsByTagName("author")->item(0)->nodeValue;
	echo "$title by $author\n";
}
?>
{% endhighlight %}

The example will output something like:

```
Found 2 books starting with 'PHP':
PHP Basics by Jim Smith
PHP Secrets by Jenny Smythe
```

### <span id="ex2">Example #2: Restricting the functions available to XPath</span>

To restrict the functions made available to XPath, provide either a string
containing the name of the single function that you wish to allow or an array
of strings containing function names as the <var>restrict</var> parameter
(note: static methods can also be used, e.g. "<samp>Classname::method</samp>").
If functions were added with <var>restrict</var> and a function is called in
XPath which is not one of them, an <var>E_WARNING</var> will be raised stating
<samp>Not allowed to call handler '<var>function</var>()'</samp> (where
<var>function</var> is the name of the function that cannot be called).

{% highlight php %}
<?php
$dom = new DOMDocument;
$dom->load('book.xml');

$xpath = new DOMXPath($dom);

// Register the php: namespace (required)
$xpath->registerNamespace("php", "http://php.net/xpath");

// Register PHP functions (no restrictions)
$xpath->registerPHPFunctions("strtoupper");

// Get first book's title in uppercase
$title = $xpath->evaluate('php:functionString("strtoupper", //book[1]/title)');
echo $title;

// Try a function not in our restrictions list
$fail = $xpath->evaluate('php:functionString("strtolower", //book[1]/title)');

?>
{% endhighlight %}

The example will output something like:

<pre>PHP BASICS<br />
<b>Warning</b>:  DOMXPath::evaluate() [<a href='domxpath.evaluate'>domxpath.evaluate</a>]: Not allowed to call handler 'strtolower()'. in <b>example2.php</b> on line <b>18</b><br />
</pre>

### <span id="ex3">Example #3: Passing DOM objects using `php:function()`</span>

Up to now, the examples have both used `php:functionString()`. As mentioned
above, instead of passing a string value to the PHP function it is possible
to pass along an array of DOM* objects to manipulate them as you please by
using `php:function()`.

{% highlight php %}
<?php
$dom = new DOMDocument;
$dom->load('book.xml');

$xpath = new DOMXPath($dom);

// Register the php: namespace (required)
$xpath->registerNamespace("php", "http://php.net/xpath");

// Register PHP functions (no restrictions)
$xpath->registerPHPFunctions("example3");

function example3($nodes) {
    // Return true if more than one author
    return count($nodes) > 1;
}
// Filter books with multiple authors
$books = $xpath->query('//book[php:function("example3", author)]');

echo "Books with multiple authors:\n";
foreach ($books as $book) {
    echo $book->getElementsByTagName("title")->item(0)->nodeValue . "\n";
}
?>
{% endhighlight %}

The example will output something like:

```
Books with multiple authors:
PHP Basics
```

### Summary

Just to quickly summarise everything, here is a quick run-down. In PHP, make
sure to register the <kbd>php</kbd> namespace (with the URI
<kbd>http://php.net/xpath</kbd>) and then register your PHP functions
(whether core, extensions or user-defined) or static methods with
`DOMXPath::registerPHPFunctions()`. In XPath, use `php:functionString()` or
`php:function()` to call the PHP function.

If you have made use of this feature, or want to know more, then do feel free
to comment. Thanks for reading.

#### Footnotes

1. <span id="fn1">[http://php.net/dom][dom]</span>
1. <span id="fn2">[http://schlitt.info/opensource/blog/0704_xpath.html][schlitt]</span>
1. <span id="fn3">The documentation page, is available at [http://php.net/domxpath.registerphpfunctions][register]</span>
1. <span id="fn4">In truth, some functions are not suitable such as those that return non-scalar values (and cannot be cast to one) which XPath will not understand.</span>


 [fn1]: #fn1 "footnote 1"
 [fn2]: #fn2 "footnote 2"
 [fn3]: #fn3 "footnote 3"
 [fn4]: #fn4 "footnote 4"
 [ex2]: #ex2 "example 2"
 [ex3]: #ex3 "example 3"
 [substr]: http://php.net/substr
 [dom]: http://php.net/dom "PHP: DOM"
 [schlitt]: http://schlitt.info/opensource/blog/0704_xpath.html "XPath overview"
 [register]: http://php.net/domxpath.registerphpfunctions "PHP: DOMXPath::registerPHPFunctions"
