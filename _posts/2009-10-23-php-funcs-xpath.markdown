---
layout: post
title: Using PHP Functions in XPath Expressions
excerpt: The <code>DOMXPath::registerPHPFunctions()</code> method is available as
  of PHP 5.3.0 and allows the use of PHP functions (and static methods)
  within XPath queries to complement the normal set of XPath functions.
---
Disclaimer: this article expects familiarity with using the DOM<sup><a href="#fn1" title="footnote 1">1</a></sup> extension and XPath<sup><a href="#fn2" title="footnote 2">2</a></sup> expressions. 

The (<del datetime="2009-10-26T14:15:44Z">currently undocumented</del><ins datetime="2009-10-26T14:15:44Z">now documented</ins><sup><a href="#fn3" title="footnote 3">3</a></sup>) <code>DOMXPath::registerPHPFunctions</code> method is available as of PHP 5.3.0 (it was added to the code base back in December 2006) and allows the use of PHP functions (and static methods) within XPath queries to complement the normal set of XPath functions<sup><a href="#fn2" title="footnote 2">2</a></sup>.<a id="more"></a><a id="more-336"></a>

## Description

<code>void DOMXPath::registerPHPFunctions  ([ string|array <var>$restrict</var>] )</code>

Enables the use of PHP functions as XPath functions.

## Parameters

<dl>
<dt>restrict</dt>
<dd>
Use this parameter to only allow certain functions to be called from XPath; it can be either a string (a function name) or an array of function names.
</dd>
</dl>

## Return Values

No value is returned.

## Examples

**Note:** The following examples load a sample XML document called <kbd>book.xml</kbd> with the following contents:

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

<h3 id="ex1">Example #1: Call a PHP function in XPath with `php:functionString`</h3>

This example demonstrates the basic use of `DOMXPath::registerPHPFunctions` by replicating the `substring` XPath function. The first thing that needs to be done is to register the <samp>php</samp> namespace with the associated URI <samp>http://php.net/xpath</samp>. Don't question it, it just needs to be done!

Next, we call `DOMXPath::registerPHPFunctions` on our object. If no arguments are used, as in this example, then the range of functions allowed to be called is not restricted—you can call any<sup><a href="#fn4" title="footnote 4">4</a></sup> function from XPath. I would always advise restricting the functions which can be called, see <a href="#ex2" title="Example 2">example 2</a>.

Within our XPath query, we use `php:functionString` which allows us to name a function and provide some parameters (or indeed, no parameters) to be passed to that function. There are two flavours which can be used here: `php:functionString` which passes an XML node/attribute as a string and `php:function` (see <a href="#ex3" title="Example 3">example 3</a>) which passes an array of XML node/attribute objects (in `DOMElement` / `DOMAttr` / etc. form) to the function .  In this example the PHP function <a href="http://php.net/substr">`substr`</a> is called, passing along the book's title (in string form), an offset of <samp>0</samp> and length of <samp>3</samp>. This returns the first 3 characters of the book's title which is then compared to the string <kbd>PHP</kbd> in order to filter our list of books down to those having titles starting with <kbd>PHP</kbd>.

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

<h3 id="ex2">Example #2: Restricting the functions available to XPath</h3>

To restrict the functions made available to XPath, provide either a string containing the name of the single function that you wish to allow or an array of strings containing function names as the <var>restrict</var> parameter (note: static methods can also be used, e.g. "<samp>Classname::method</samp>").  If functions were added with <var>restrict</var> and a function is called in XPath which is not one of them, an <var>E_WARNING</var> will be raised stating <samp>Not allowed to call handler '<var>function</var>()'</samp> (where <var>function</var> is the name of the function that cannot be called).

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
<b>Warning</b>:  DOMXPath::evaluate() [<a href='domxpath.evaluate'>domxpath.evaluate</a>]: Not allowed to call handler 'strtolower()'. in <b>example2.php</b> on line <b>18</b><br /></pre>

<h3 id="ex3">Example #3: Passing DOM objects using `php:function`</h3>

Up to now, the examples have both used `php:functionString`. As mentioned above, instead of passing a string value to the PHP function it is possible to pass along an array of DOM* objects to manipulate them as you please by using `php:function`.

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

<pre>Books with multiple authors:
PHP Basics
</pre>

### Summary

Just to quickly summarise everything, here is a quick run-down. In PHP, make sure to register the <kbd>php</kbd> namespace (with the URI <kbd>http://php.net/xpath</kbd>) and then register your PHP functions (whether core, extensions or user-defined) or static methods with `DOMXPath::registerPHPFunctions`. In XPath, use `php:functionString` or `php:function` to call the PHP function.

If you have made use of this feature, or want to know more, then do feel free to comment. Thanks for reading.

#### Footnotes

<ol>
<li id="fn1"><a href="http://php.net/dom" title="PHP: DOM">http://php.net/dom</a></li>
<li id="fn2"><a href="http://schlitt.info/opensource/blog/0704_xpath.html" title="XPath overview">http://schlitt.info/opensource/blog/0704_xpath.html</a>
<li id="fn3">The documentation page, <del datetime="2009-10-26T14:15:44Z">when it gets written, will be</del><ins datetime="2009-10-26T14:15:44Z">is</ins> available at <a href="http://php.net/domxpath.registerphpfunctions" title="PHP: DOMXPath::registerPHPFunctions">http://php.net/domxpath.registerphpfunctions</a></li>
<li id="fn4">In truth, some functions are not suitable such as those that return non-scalar values (and cannot be cast to one) which XPath will not understand.</li>
</ol>
