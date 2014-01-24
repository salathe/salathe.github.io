---
layout: post
title: Server download speed using PHP cURL
excerpt: Using cURL to get an idea of the download speed of your web server.
---
I've been meaning to add a few snippets of code to the blog, just to archive
them to save my own memory but who knows, they may prove useful to someone else.

This particular one uses cURL to get an idea of the download speed of your web
server but is more a demonstration of grabbing the extra information about a
cURL transaction which people rarely take a look at. The code was first posted
in a similar topic [on Sitepoint][sitepoint].

## PHP Snippet

{% highlight php %}
<?php error_reporting(E_ALL | E_STRICT);

// Initialize cURL with given url
$url = 'http://download.bethere.co.uk/images/61859740_3c0c5dbc30_o.jpg';
$ch = curl_init($url);

curl_setopt($ch, CURLOPT_HEADER, 0);
curl_setopt($ch, CURLOPT_USERAGENT, 'Sitepoint Examples (thread 581410; http://www.sitepoint.com/forums/showthread.php?t=581410)');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 2);
curl_setopt($ch, CURLOPT_TIMEOUT, 60);

set_time_limit(65);

$execute = curl_exec($ch);
$info = curl_getinfo($ch);

// Time spent downloading, I think
$time = $info['total_time'] 
      - $info['namelookup_time'] 
      - $info['connect_time'] 
      - $info['pretransfer_time'] 
      - $info['starttransfer_time'] 
      - $info['redirect_time'];


// Echo friendly messages
header('Content-Type: text/plain');
printf("Downloaded %d bytes in %0.4f seconds.\n", $info['size_download'], $time);
printf("Which is %0.4f mbps\n", $info['size_download'] * 8 / $time / 1024 / 1024);
printf("CURL said %0.4f mbps\n", $info['speed_download'] * 8 / 1024 / 1024);

echo "\n\ncurl_getinfo() said:\n", str_repeat('-', 31 + strlen($url)), "\n";
foreach ($info as $label => $value)
{
	printf("%-30s %s\n", $label, $value);
}
{% endhighlight %}

## Example Output

    Downloaded 6576848 bytes in 16.2153 seconds.
    Which is 3.0944 mbps
    CURL said 3.0755 mbps


    curl_getinfo() said:
    ---------------------------------------------------------------------------------------------
    url                            http://download.bethere.co.uk/images/61859740_3c0c5dbc30_o.jpg
    content_type                   image/jpeg
    http_code                      200
    header_size                    263
    request_size                   198
    filetime                       -1
    ssl_verify_result              0
    redirect_count                 0
    total_time                     16.314966
    namelookup_time                0.000287
    connect_time                   0.021524
    pretransfer_time               0.021595
    size_upload                    0
    size_download                  6576848
    speed_download                 403117
    speed_upload                   0
    download_content_length        6576848
    upload_content_length          0
    starttransfer_time             0.056275
    redirect_time                  0

As you can see from the example output, there's a miniature treasure trove of
information about the cURL transaction which is particularly helpful for the
purposes of reporting on the speed and other statistics of the download.
Do you ever bother to take a look at the information from your cURL calls?

 [sitepoint]: http://www.sitepoint.com/forums/showthread.php?t=581410
