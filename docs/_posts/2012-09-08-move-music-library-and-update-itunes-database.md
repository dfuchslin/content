---
id: 179
title: 'Move music library and update iTunes database'
date: '2012-09-08T20:55:07+02:00'
author: David
layout: post
guid: 'http://gyttja.wordpress.com/?p=179'
permalink: /2012/09/08/move-music-library-and-update-itunes-database/
categories:
    - OSX
tags:
    - itunes
---

I'm doing some reorganization of my network shares. My music is saved on the server under its own share, "Music". In the new scheme, I want the music folder to be located in a subfolder in the share "Multimedia". This poses a small problem: I have to update iTunes to recognize the new file locations. I've got iTunes 10.6.3 on OS X Mountain Lion 10.8.1.

<!--more-->

<h2>Edit iTunes Music Library.xml</h2>
The simple solution is to modify the Location values in the <code>iTunes Music Library.xml</code> file, mangle the <code>iTunes Library.itl</code> file, then open iTunes. iTunes will then rebuild the database file based on the xml (the .itl file is the active database file, the .xml file is regenerated based on the .itl database).  To find and replace all the locations I tried this:

<pre language="bash">
cat ~/Music/iTunes/iTunes Music Library.xml | perl -pe 's//Volumes/Music///Volumes/Multimedia/music//i' &gt; itunes.xml
</pre>

Then quickly checked to see if I was missing any other locations:

<pre language="bash">
cat itunes.xml | grep &quot;Location&quot; | grep -v &quot;/Volumes/Multimedia/music&quot;
</pre>

Then erase <code>iTunes Library.itl</code>, replace <code>iTunes Music Library.xml</code> with this new copy (making backups of the originals first, of course).

An unfortunate side effect of rebuilding the .itl based on the xml: the Date Added values for the entire library are reset (probably other values are reset as well). I wanted to move my library files and keep all the metadata intact.
<h2>Edit iTunes Library.itl</h2>
So, instead of editing the xml file, what about editing the itl file? Unfortunately, the .itl file is a proprietary format binary file. Luckily, there are some who have tinkered with the file in order to edit .itl database entries. Enter <a title="titl - Tools for iTunes Libraries" href="http://code.google.com/p/titl/" target="_blank">Tools for iTunes Libraries (titl)</a>: excellent! I cloned the mercurial repository, built the code and tried to move some files:

<pre language="bash">
$ hg clone https://code.google.com/p/titl/ titl
$ cd titl
$ mvn verify
$ java -Xmx512m -XX:MaxPermSize=256m -jar titl-core/target/titl-core-0.3-SNAPSHOT.jar MoveMusic --use-urls ~/Music/iTunes/iTunes Library.itl &quot;file://localhost/Volumes/Music&quot; &quot;file://localhost/Volumes/Multimedia/music&quot;
</pre>

That resulted in a <code>Exception in thread "main" java.io.EOFException</code>, the exact same issue as <a title="titl - EOFException when using MoveMusic" href="http://code.google.com/p/titl/issues/detail?id=18" target="_blank">this one</a>. Downloaded the patch file that the user thankfully uploaded, applied the patch to the code, and tried again (disabling the now broken unit tests with <code>-Dmaven.test.skip=true</code>): success! Excellent!

Final step: rename the <code>iTunes Library.itl.processed</code> file to <code>iTunes Library.itl</code> (making backup first of the original file of course). iTunes works as expected, music files are found, play count still there, "last added" dates still there.

Not that I use iTunes very often (or really at all) anymore to play music. Spotify is the scheisse these days! ;)

<em>Updated 2011-12-16: Uploaded the <a href="http://dl.dropbox.com/u/381583/titl-core-0.3-SNAPSHOT.zip" title="titl-core-0.3-SNAPSHOT.zip" target="_blank">patched + compiled jar</a> (for those of you who want it)</em>