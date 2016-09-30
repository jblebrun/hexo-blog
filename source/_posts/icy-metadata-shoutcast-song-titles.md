---
title: ICY MetaData (ShoutCast song titles)
tags:
  - coding
  - music
  - python
  - shoutcast
id: 9
categories:
  - Hacking
date: 2006-12-31 14:06:21
---

I wrote a quick python script once to help me parse song metadata from ICY streams. Quite a few popular streams use this format these days, including most of the streams on ShoutCast and IceCast. 

<!--more-->

The gory details about how the stream metadata is encoded can be found [here](http://www.smackfu.com/stuff/programming/shoutcast.html).

1.  Add "ICY Metadata: 1" to the HTTP GET headers.
2.  Parse the icy-metaint from the HTTP response headers.
3.  Begin the stream
4.  Every icy-metaint bytes, parse metadata (first byte is length/16)
5.  Update your music player with the information.
I originally investigated this because I want better handling of ShoutCast streams in Window Media Center. In the time since I originally did this research, there has been a[ plug-in developed by mcTools called "mcShoutcast"](http://en.mcetools.de/), however it doesn't integrate the metadata with the core media player views, which means you can't do things like watch visualizations and have the song information appear as it changes.

My big question: Is it possible to force the change of what's displayed in the now playing box in Windows Media Center?
Here's my simple python script that will get the meta data for what's currently playing on Groove Salad. I made it based on the info found [here](http://www.smackfu.com/stuff/programming/shoutcast.html).
<pre lang="python">import urllib2

request = urllib2.Request('http://scfire-dll0l-2.stream.aol.com/stream/1018')
request.add_header('Icy-MetaData','1')
opener = urllib2.build_opener()

data=opener.open(request)
headers=True
while headers:
    line = data.readline()
    if line[0:11]=="icy-metaint":
	interval = int(line[13:])

    if line=="\r\n":
	headers=False

data.read(interval)
len=ord(data.read(1))*16
print "Meta data is",len
print data.read(len)</pre>