---
layout: post
title: Tiny Little GIFs
excerpt: Thirty-six little bytes, and nothing much to see. My attempt
  at creating the tiniest transparent GIF image possible.
---
Early this morning, Paul Bonser write a neat little article
([The Tiniest GIF ever][bonser]) on his blog about reducing the size of GIF
images to their bare minimum number of bytes.

Starting off with a 43 byte image produced in image editor GIMP, he describes
the process of removing bytes that aren't particularly necessary with reference
to the [GIF specification][gif].  The end result is a working, fully transparent
GIF image in only 37 bytes with two further size reductions producing a fixed
colour (35 bytes) and an "I'm feeling lucky" colour (26 bytes).

Since it's a Sunday, and I had nothing better to do, I also figured it was about
time to delve into the guts of GIF images and see if I could come to the same
conclusions that Paul had with regards to putting GIF on a diet.

Starting with a 43 byte, single-pixel, transparent GIF exported from Photoshop
(sorry no GIMP installed on here) I followed the same process as Paul.  Because
I'm a PHP coder, that made sense to me to do the byte tweaking—sure a hex editor
would do fine, but PHP is my chosen tool.  Sure enough, thanks to the great
instructions I was able to reproduce exactly the same results as Paul found and
more importantly, now I know precisely how GIF images are constructed which is
something I didn't know (other than a basic idea of there being a header,
image data, etc.) before today.

I also arrived at the same conclusion as Paul, after much byte-pushing and
shoving. 26 bytes is as small as things are going to get (until someone more
brainy than the both of us shares their own findings).

On the transparent GIF, Paul said, "So there you go, the tiniest **transparent**
GIF possible (if you can make one smaller, let me know)."  It's not much but I
did manage to eek out an extra byte from the 37 byte transparent image by
removing one byte out of the LZW compressed image data portion of the file.

So there you go, the tiniest transparent GIF possible at **36** bytes. (Again,
like Paul, do let me know if you can make one smaller!).

Another interesting tidbit gleaned from this experimentation is that the
transparent image can be any pixel size up to 65535 square and still remain
at the 36 bytes.  Quite what one would do with a 4,294,836,225 pixel transparent
GIF, I'm not sure.

If, for any reason, you want to get a hand on those 36 little bytes, here they
are in two formats:

1. **Hex Bytes**:

        47 49 46 38 39 61 01 00 01
        00 00 00 00 21 f9 04 01 00
        00 00 00 2c 00 00 00 00 01
        00 01 00 00 01 01 01 00 3b

1. **GIF Image**: [download here][file] (it's not much to look at!)


Anyhow, that's enough frivolity for a Sunday evening. Back to watching the
wildlife documentary on TV. :)

 [bonser]: http://blog.paulbonser.com/2009/03/15/the-tiniest-gif-ever/
 [gif]: http://www.w3.org/Graphics/GIF/spec-gif89a.txt
 [file]: /public/files/1px-trans.gif
