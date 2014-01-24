---
layout: post
title: Glob Patterns for File Matching in PHP
excerpt: Overview of the wildcard patterns that can be used with <code>glob()</code>.
---
This article has been on the cards for a while now with recent articles
elsewhere<sup>[1][fn1],[2][fn2]</sup> prompting me to get this finished and up
on the blog. The focus here is on the wildcard patterns that can be used
with `glob`.


## Intro to the <code>glob</code> function

To quote the succinct description on the [PHP manual page][glob] for `glob`:

> The `glob` function searches for all the pathnames matching <var>pattern</var>
> according to the rules used by the libc `glob` function, which is similar to
> the rules used by common shells.
>
> – <cite>http://php.net/glob</cite>

## What's in a pattern?

Most people who have already encountered `glob` know to make use of
the `*` metacharacter to match _some characters_, and those digging a little
deeper often discover that discrete alternatives can be globbed with braces
(e.g. `image.{gif,jpg,png}`). However, there are more special characters and 
sequences that can be used to be more (or less, if we want) specific about 
what to find. 

<small>Aside: please **do not** make the mistake of thinking that glob
patterns are _regular expressions_, they're just not. If you do want to use
regular expressions to find paths/files then you are invited to use SPL's
[RegexIterator][regexiterator], which allows filtering of an Iterator based on
a PCRE regex, in conjunction with a DirectoryIterator or FilesystemIterator
(there are recursive flavours of the Regex- and DirectoryIterator if you need
to delve into folders). For those SPL-ly inclined, also note the 
[GlobIterator][globitertor] which combines the goodness of globbing with 
iteration. If that made entirely _no sense_, please read on!
Globs are much less verbose.</small>

So, here are the special doohickeys (technical term!) that we can use
with `glob`:

<dl>
  <dt><code>*</code> (an asterisk)</dt>
  <dd>
    Matches zero of more characters.
  </dd>
  <dt><code>?</code></dt>
  <dd>
    Matches exactly any one character.
  </dd>
  <dt><code>[...]</code></dt>
  <dd>
    Matches one character from a group.  A group can be a list of characters,
    e.g. <code>[afkp]</code>, or a range of characters,
    e.g. <code>[a-g]</code> which is the same as <code>[abcdefg]</code>.
  </dd>
  <dt><code>[!...]</code></dt>
  <dd>
    Matches any single character not in the group. <code>[!a-zA-Z0-9]</code>
    matches any character that is not alphanumeric.
  </dd>
  <dt><code>\</code></dt>
  <dd>
    Escapes the next character. For special characters, this causes them to
    not be treated as special. For example, <code>\[</code> matches a literal
    <samp>[</samp>. If <var>flags</var> includes <var>GLOB_NOESCAPE</var>,
    this quoting is disabled and <code>\</code> is handled as a
    simple character.
  </dd>
</dl>

## Globbingly good glob examples

Here are a few examples of what globs might look like alongside a brief
description of the intended behaviour: if you have any suggestions please do
make them in the comments as I'm running short on inspiration! 

<table>
  <thead>
    <tr style="width:20%">
      <th>pattern</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>*.txt</code></td>
      <td>
        Get directory contents which have the extension of <samp>.txt</samp>
        (Note: a file could be named simply <samp>.txt</samp>!).
      </td>
    </tr>
    <tr>
      <td><code>??</code></td>
      <td>
        Get directory contents with names _exactly_ two characters in length.
      </td>
    </tr>
    <tr>
      <td><code>??*</code></td>
      <td>
        Get directory contents with names _at least_ two characters in length.
      </td>
    </tr>
    <tr>
      <td><code>g?*</code></td>
      <td>
        Get directory contents with names at least two characters in length
        and starting with the letter <samp>g</samp>
      </td>
    </tr>
    <tr>
      <td><code>*.{jpg,gif,png}</code></td>
      <td>
        Get directory contents with an extension of <samp>.jpg</samp>,
        <samp>.gif</samp> or <samp>.png</samp>. Remember to use the
        <var>GLOB_BRACE</var> flag.
      </td>
    </tr>
    <tr>
      <td><code>DN?????.dat</code></td>
      <td>
        Get directory contents which start with the letters <samp>DN</samp>,
        followed by five characters, with an extension of <samp>.dat</samp>.
      </td>
    </tr>
    <tr>
      <td><code>DN[0-9][0-9][0-9][0-9][0-9].dat</code></td>
      <td>
        Get directory contents which start with the letters <samp>DN</samp>,
        followed by five _digits_, with an extension of <samp>.dat</samp>.
      </td>
    </tr>
    <tr>
      <td><code>[!aeiou]*</code></td>
      <td>Get directory contents which do not start with a vowel letter.</td>
    </tr>
    <tr>
      <td><code>[!a-d]*</code></td>
      <td>
        Get directory contents which do not start with <samp>a</samp>,
        <samp>b</samp>, <samp>c</samp> or <samp>d</samp>.
      </td>
    </tr>
    <tr>
      <td><code>*\[[0-9]\].*</code></td>
      <td>
        Get directory contents whose basename ends with a single digit
        enclosed in square braces. If <var>GLOB_NOESCAPE</var> is used, a
        single digit enclosed in <samp>\[</samp> and <samp>\]</samp> which
        would be a pretty weird name.
      </td>
    </tr>
    <tr>
      <td><code>subdir/img*/th_?*</code></td>
      <td>
        Get directory contents whose name starts with <samp>th_</samp>
        (with at least one character after that) within directories whose
        names start with <samp>img</samp> in the <samp>subdir</samp> directory.
      </td>
    </tr>
  </tbody>
</table>

Well there we go, I've said what I came here to say so all that remains to be
done is give some link love to those two recent articles that prompted me to
dust off this draft and click the "publish" button. 

### With thanks

1. <span id="fn1"></span>
   php|architect's [Putting glob() to the test][phpa_glob]
   focuses on execution times of `glob` and other techniques of getting a
   list of files.
2. <span id="fn2"></span>
   Marcus Schumann's [Loop Through Folders with PHP's Glob()][nettuts_glob]
   article on Nettuts+ gives a brief introduction for those who might not have
   been introduced to `glob`.

  [fn1]: #fn1 "php|architect - Putting glob() to the test")
  [fn2]: #fn2 "Nettuts+ - Loop Through Folders with PHP's Glob()"
  [glob]: http://php.net/glob "PHP: Glob"
  [regexiterator]: http://php.net/regexiterator "PHP: SPL RegexIterator"
  [globiterator]: http://php.net/globiterator "PHP: SPL GlobIterator"
  [phpa_glob]: http://www.phparch.com/2010/04/28/putting-glob-to-the-test/
  [nettuts_glob]: http://net.tutsplus.com/tutorials/php/quick-tip-loop-through-folders-with-phps-glob/
