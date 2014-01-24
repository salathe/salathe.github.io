---
layout: post
title: 'PHP Variable Names: Curly Brace Madness'
excerpt: Having fun with a quirkier side of PHP syntax.
---
Most PHP coders will know that one is able to specify variable names in the
following format, generally within double-quoted strings, however much
they frown on it:

{% highlight php startinline %}
$str = "Hello ${username}, you look nice today.";
{% endhighlight %}

However, that's not the only way to use the brace syntax!

Straying away from that common usage, it is perfectly reasonable to use the
same format in a normal assignment operation:

{% highlight php startinline %}
// Same as $str = 'Hello Peter';
${'str'} = 'Hello Peter';
{% endhighlight %}

Now, moving into the bizzare, the string value within the curly braces can
be any expression which results in a [valid variable name][variables]
(and more, see later)!  

{% highlight php startinline %}
// Each are the same as $str = 'Hello Peter';
${'s'.'t'.'r'} = 'Hello Peter';
${substr('string', 0, 3)} = 'Hello Peter';
${ 'a' == 'b' ? 'rts' : 'str' } = 'Hello Peter';
{% endhighlight %}

Finally, lets get really crazy.  The strings within the curly braces
needn't necessarily strictly adhere to the normal variable naming conventions!
Since the curly braces literally mean "evaluate my contents as a string", we
can play around even more!

{% highlight php startinline %}
// You can't access these with 'normal' global variables, 
// but the curly brace form works as does getting at them 
// via the $GLOBALS superglobal variable!
// Values are converted to strings which is why 0xFF => '255', etc.
${007} = 'Hello Peter'; // $GLOBALS['7']
${0xFF} = 'Hello Peter'; // $GLOBALS['255']
${2 + 2} = 'Hello Peter'; // $GLOBALS['4']
${'Hello Peter'} = 'Hello Peter'; // $GLOBALS['Hello Peter']
${'cats, pet food, dogs'} = 'Hello Peter'; // $GLOBALS['cats, pet food, dogs']
{% endhighlight %}

I'm pretty sure these weird variable names serve absolutely no practical
purpose, but it's interesting to know of little quirks like this (well,
interesting for weirdos like me).  Have a play around yourself, remembering
that you can `print_r()` or `var_dump()` the superglobal `$GLOBALS` or even
use `get_defined_vars()` to find out the variable names that have been created.

 [variables]: http://php.net/variables#language.variables.basics
