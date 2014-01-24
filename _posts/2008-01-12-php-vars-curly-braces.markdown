---
layout: post
title: 'PHP Variable Names: Curly Brace Madness'
excerpt: Having fun with a quirkier side of PHP syntax.
---
Most PHP coders will know that one is able to specify variable names in the following format, generally within double-quoted strings, however much they frown on it:

<pre lang="PHP">$str = "Hello ${username}, you look nice today.";</pre>

However, that's not the only way to use the brace syntax!

<a id="more"></a><a id="more-18"></a>Straying away from that common usage, it is perfectly reasonable to use the same format in a normal assignment operation:

<pre lang="PHP">// Same as $str = 'Hello Peter';
${'str'} = 'Hello Peter';</pre>

Now, moving into the bizzare, the string value within the curly braces can be any expression which results in a <a href="http://php.net/variables#language.variables.basics">valid variable name</a> (and more, see later)!  

<pre lang="PHP">// Each are the same as $str = 'Hello Peter';
${'s'.'t'.'r'} = 'Hello Peter';
${substr('string', 0, 3)} = 'Hello Peter';
${ 'a' == 'b' ? 'rts' : 'str' } = 'Hello Peter';
</pre>

Finally, lets get really crazy.  The strings within the curly braces needn't necessarily strictly adhere to the normal variable naming conventions!  Since the curly braces literally mean "evaluate my contents as a string", we can play around even more!

<pre lang="PHP">// You can't access these with 'normal' global variables, 
// but the curly brace form works as does getting at them 
// via the $GLOBALS superglobal variable!
// Values are converted to strings which is why 0xFF => '255', etc.
${007} = 'Hello Peter'; // $GLOBALS['7']
${0xFF} = 'Hello Peter'; // $GLOBALS['255']
${2 + 2} = 'Hello Peter'; // $GLOBALS['4']
${'Hello Peter'} = 'Hello Peter'; // $GLOBALS['Hello Peter']
${'cats, pet food, dogs'} = 'Hello Peter'; // $GLOBALS['cats, pet food, dogs']
</pre>

I'm pretty sure these weird variable names serve absolutely no practical purpose, but it's interesting to know of little quirks like this (well, interesting for weirdos like me).  Have a play around yourself, remembering that you can <code>print_r()</code> or <code>var_dump()</code> the superglobal <code>$GLOBALS</code> or even use <code>get_defined_vars()</code> to find out the variable names that have been created.
