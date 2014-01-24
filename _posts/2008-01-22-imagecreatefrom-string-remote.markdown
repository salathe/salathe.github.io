---
layout: post
title: PHP Imagecreate & Different File Types
excerpt: Using <code>imagecreatefromstring()</code> to create a GD image.
---
For a recent little bit of PHP scripting, I wanted to grab an image off of a remote server (not for any nefarious purpose!) and manipulate it in PHP (with <a href="http://php.net/image">GD</a>).  <a id="more"></a><a id="more-20"></a>The actual manipulation was trivial, all I wanted to do was reduce the dimensions of the remote image, so I won't go into that in this post.

However, when thinking about getting the remote file and making it a GD image there were a few options available.  The 'normal' methods of creating GD images from files seemed like a reasonable start: imagecreatefrom<em>filetype</em>, where <em>filetype</em> is one of <em>png, jpeg, gif</em> (or even gd2, wbmp, xbm, xpm...).  At first glance everything seems cool, these functions can even grab the file from remote servers. Assuming http://example.org/photo.jpg is a valid image, then <code>imagecreatefromjpeg('http://example.org/photo.jpg');</code> would work fine (if <code>allow_url_fopen</code> is enabled in php.ini).

<h3>First Hurdle</h3>

The first problem that came to light was that I never really knew what kind of image would be returned from the remote server.  All of the remote image names were <em>something</em>.gif but the actual image could be anything from a gif to jpeg to png (the most common web image types).  Determining the image type isn't particularly trivial, you can easily grab the MIME type from the remote server and use the appropriate <code>imagecreatefrom...</code> function but there's no guaranteeing that the server would necessarily throw out the correct MIME (from testing, this didn't appear to be a problem but one must always assume that the MIME can be telling fibs).  

I could have ploughed ahead, assuming everything was all ok and grab the remote image in a <code>switch</code> statement (or series of <code>if</code>s, etc.) like so:
<pre lang="PHP">
$remote_file = 'http://www.talkphp.com/avatars/salathe.gif';
$headers = get_headers($remote_file, 1);

switch ($headers['Content-Type'])
{
	case 'image/jpeg':
		$image = imagecreatefromjpeg($remote_file);
	break;
	case 'image/gif':
		$image = imagecreatefromgif($remote_file);
	break;
	case 'image/png':
		$image = imagecreatefrompng($remote_file);
	break;
	default:
		die('Invalid image type');
}
</pre>

Now, that's a huge mess and whilst things could easily be tidied up by using <em>variable functions</em> or some other structure like that but the problem would still exist of looking at the mime type to determine which function to use.  Wouldn't it be great if there was a function which would create an image regardless of the image type fed into it?  Worry not, such a function exists (but strangely isn't discussed very often).

<h3>Introducing Imagecreatefromstring()</h3>

Grabbing the remote image from the server is really easy using <code>file_get_contents</code> (or even cURL if you like). Once you have the file's raw contents, those can be fed directly into <code>imagecreatefromstring</code> and the function will return a nice, new GD image. Easy peasy!

If you want to be really, really concise (forgetting error handling, etc. for a moment) it's a simple one-liner:
<pre lang="php">
$image = imagecreatefromstring(file_get_contents('http://www.talkphp.com/avatars/salathe.gif'));
</pre>

How cool is that? No need for ugly MIME type checking and calling different functions based on that; the <code>imagecreatefromstring</code> function can autodetect JPEG, PNG, GIF, WBMP, and GD2 images (so long as your PHP build supports whichever type) without any hassle.

A final note, there are comments around regarding the memory consumption of <code>imagecreatefromstring</code> and I have to admit that I've really not tested that side of things at all. I like to use a lot of caching on my side of things anyway, so the number of times that <code>imagecreatefromstring</code> is being called in the script I mentioned at the beginning of this post is very infrequent: in the region of once every couple of days! The main point here was to offer up an alternative solution, which may or may not be useful to you at some point. 
