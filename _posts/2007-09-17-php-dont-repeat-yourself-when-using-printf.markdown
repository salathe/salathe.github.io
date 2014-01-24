---
layout: post
title: Don't repeat yourself when using printf!
excerpt: Using numbered placeholders to reuse argument values.
---
Hi folks,

We all use <a href="http://php.net/printf">printf</a> (or <a href="http://php.net/sprintf">sprintf</a>) to help ourselves when mingling together output strings with our variables, right?  This is a tip that I use often, but I continually see people writing code where this technique would be useful but isn't used. What _am_ I on about?   <a id="more"></a><a id="more-14"></a> Well, how many times have you seen people write something similar to this:
<pre lang="php">echo '<ul>';
foreach ($aItems as $aItem)
{
    // <li><a href="link.html" title="Link 2">Link 2</a> (link.html)</li>
    printf('<li><a href="%s" title="%s">%s</a> (%s)</li>',
           $aItem['link'],
           $aItem['title'],
           $aItem['title'],
           $aItem['link']);
}
echo '</ul>';</pre>

Notice that in the <code>printf</code> call, there are a number of repeated arguments (2 x <code>$aItem['link']</code>, and 2 x <code>$aItem['title']</code>).  Wouldn't it be great if we could satisfy the lazy side within all of us and not repeat ourselves?  Well, it's possible!  Take a look at the amended example below:
<pre lang="php">printf('<li><a href="%1$s" title="%2$s">%2$s</a> (%1$s)</li>',
       $aItem['link'],
       $aItem['title']);</pre>

Ok, I admit for those of you not well versed in using this (or those who didn't look at the code very hard) might well be a little lost right now.  What's with the extra stuff messing up our nice, normal "%s" placeholders?

Well, the extra stuff is what helps us to be lazy!  The "1$" and "2$" allow us to point to the argument number which will fill in that particular placeholder.  In the code above, 1$ refers to <code>$aItem['link']</code> and 2$ refers to <code>$aItem['title']</code>.  The numbers preceding the dollar sign simply reference the order of the arguments fed into the function call.  What makes things useful for us, in this instance, is that the numbered replacements can be repeated any number of times within the template string without the need to add more (of the same) arguments as we did in the original example up above!

Finally, here is another example which hopefully should help to cement things into your own mind about using numbered placeholders in your (s)printf function calls:
<pre lang="php">$szAdjective = 'fluffy';
$szNoun = 'cat';

// Both methods will output the following:
//     Yesterday, I saw a cat. It was a fluffy cat! 
//     I have never seen a cat quite so fluffy.

// Old-school method
printf('Yesterday, I saw a %s. '.
       'It was a %s %s! I have '.
       'never seen a %s quite so %s.',
       $szNoun,
       $szAdjective,
       $szNoun,
       $szNoun,
       $szAdjective);

// Sexy, lazy method
printf('Yesterday, I saw a %1$s. '.
       'It was a %2$s %1$s! I have '.
       'never seen a %1$s quite so %2$s.',
       $szNoun,
       $szAdjective);</pre>

Be lazy, use numbered placeholders! :)
