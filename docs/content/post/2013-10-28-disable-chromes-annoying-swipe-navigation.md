---
author: David
categories:
  - OSX
date: '2013-10-28T18:15:12+01:00'
guid: https://david.gyttja.com/?p=288
id: 288
permalink: /2013/10/28/disable-chromes-annoying-swipe-navigation/
title: Disable Chrome&#8217;s annoying swipe navigation
url: /2013/10/28/disable-chromes-annoying-swipe-navigation/
---


Ok, it's not really Chrome's fault. But I really get annoyed when I happen to swipe left or right with two fingers on a page and Chrome quickly displays the previous or next page in my browser history. Safari does the same, but its UI is a little more intuitive by showing the new page glide right and the previous page appear underneath and most times you can actually catch your mistaken swipe before it's too late. Chrome's animation is brutally fast (which I suppose is a good thing, at least the fast part). In my day-to-day dealings with Chrome, however, I think it's just annoying and I want a way to turn it off.

<!--more-->

There's a global OSX touchpad setting for this (I'm still running Mountain Lion, but I assume Mavericks is the same), "Swipe between pages".
<a href="/images/2013/10/pageswipe.png"><img src="/images/2013/10/pageswipe-300x247.png" alt="pageswipe" width="300" height="247" class="alignnone size-medium wp-image-289" /></a>

But as I said, I just want to turn it off for Chrome. The solution:
<pre code="bash">
$ defaults write com.google.Chrome.plist AppleEnableSwipeNavigateWithScrolls -bool FALSE
</pre>
Then restart Chrome. Done. Nice :)
