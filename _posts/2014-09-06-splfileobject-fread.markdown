---
layout: post
title: SplFileObject::fread()
excerpt: PHP 5.5.11 introduced a new method to the useful <code>SplFileObject</code>
  class, in order to make reading the whole file easier.
---
Let's go back to August 2013 for a moment. This was when Sherif Sabry (probably
not the [Egyptian tennis player](http://en.wikipedia.org/wiki/Sherif_Sabry_%28tennis%29))
raised a ticket on the PHP bug tracker:

 > [Request #65545 Implement file_get_contents() method for SplFileObject](https://bugs.php.net/bug.php?id=65545)

A few months passed and [Tjerk Anne Meesters](http://people.php.net/datibbaw) put together
a patch, and eventually committed the new `SplFileObject::fread()` method to the
PHP source; which became publicly available in PHP 5.5.11.

## Expected usage

The method's primary purpose is to be the `SplFileObject`-based alternative
to the [`fread()`](http://php.net/fread) function (for all of the OOP groupies),
and also to provide a way to replicate the [`file_get_contents()`](http://php.net/file_get_contents) function for `SplFileObject` objects; to
easily read the entire contents of a file.

{% highlight php %}
<?php
$file = new SplFileObject('/path/to/a/file.txt');

// Replicating file_get_contents()
echo $file->fread($file->getSize());
?>
{% endhighlight %}

## Another practical example

When might reading the file in chunks of bytes, rather than normally line-by-line,
be useful? Well, here's one quicky example, which provides a hex dump of
whatever is passed to the file via stdin.

### hex_dump.php

{% highlight php %}
<?php
// Helper function to format bytes into a hex dump
function hex_dump($bytes, $width) {
    $bytes_dump = preg_replace('/[^[:print:]]/', '.', $bytes);
    $bytes_dump = str_pad($bytes_dump, $width);

    $hex_bytes = unpack('H*', $bytes)[1];
    $hex_dump  = implode(' ', str_split($hex_bytes, 2));
    $hex_dump  = str_pad($hex_dump, $width * 3);

    return "{$hex_dump} | {$bytes_dump}\n";
};

// Number of bytes to read at a time
$width = 16;

// Read from stdin and print hex dump
$stdin = new SplFileObject('php://stdin');
while ($stdin->valid()) {
    echo hex_dump($stdin->fread($width), $width);
}
?>
{% endhighlight %}

### Usage

{% highlight bash %}
$ php hex_dump.php < rhyme.txt
{% endhighlight %}

### rhyme.txt

    Mary had a little lamb,
    His fleece was white as snow,
    And everywhere that Mary went,
    The lamb was sure to go.

### Output

    4d 61 72 79 20 68 61 64 20 61 20 6c 69 74 74 6c  | Mary had a littl
    65 20 6c 61 6d 62 2c 0a 48 69 73 20 66 6c 65 65  | e lamb,.His flee
    63 65 20 77 61 73 20 77 68 69 74 65 20 61 73 20  | ce was white as 
    73 6e 6f 77 2c 0a 41 6e 64 20 65 76 65 72 79 77  | snow,.And everyw
    68 65 72 65 20 74 68 61 74 20 4d 61 72 79 20 77  | here that Mary w
    65 6e 74 2c 0a 54 68 65 20 6c 61 6d 62 20 77 61  | ent,.The lamb wa
    73 20 73 75 72 65 20 74 6f 20 67 6f 2e 0a        | s sure to go..  

## External resources

* PHP manual: [`SplFileObject::fread()` method docs](http://php.net/splfileobject.fread "SplFileObject::fread()")
* PHP manual: [`SplFileObject` class](http://php.net/splfileobject "SplFileObject")
* PHP bug tracker: [Request #65545 Implement file_get_contents() method for SplFileObject](https://bugs.php.net/bug.php?id=65545 "Request #65545 Implement file_get_contents() method for SplFileObject")
