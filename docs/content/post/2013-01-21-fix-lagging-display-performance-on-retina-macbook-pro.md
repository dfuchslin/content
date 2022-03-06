---
author: David
categories:
  - OSX
date: '2013-01-21T12:09:33+01:00'
geo_public:
  - '0'
guid: http://gyttja.wordpress.com/?p=246
id: 246
permalink: /2013/01/21/fix-lagging-display-performance-on-retina-macbook-pro/
tags:
  - pram
  - retina
title: Fix lagging display performance on retina MacBook Pro
url: /2013/01/21/fix-lagging-display-performance-on-retina-macbook-pro/
---


This weekend my Mac suddenly started behaving strangely: moving windows around occurred with a nearly psychodelic delay, mission control (aka exposé) was "jerky", and scrolling was not fun. Forcing the graphics card to the discrete NVIDIA GT 650 instead of integrated Intel GPU sped things up, but the overall experience still didn't feel right. Since the onset was sudden I immediately though: imminent hardware failure! But thankfully that turned out not to be the case.

<!--more-->

Scouring forums for answers led me <a href="https://discussions.apple.com/message/19897549#19897549%2319897549" target="_blank">here</a>, which worked for me!. The basic idea: delete some preferences files and reset the PRAM:
<ul>
	<li>Delete /Library/Preferences/com.apple.windowserver.plist</li>
	<li>Delete ~/Library/Preferences/ByHost/com.apple.windowserver*.plist</li>
	<li>Shutdown OSX</li>
	<li>Startup, immediately press and hold the P and R keys while holding down the option (⌥) and command (⌘) keys before the gray boot screen appears, which <a href="http://support.apple.com/kb/HT1379" title="reset PRAM" target="_blank">resets the PRAM</a></li>
	<li>You may have to reset your display preferences (resolution) once you login</li>
	<li>Done!</li>
</ul>

<em>2013-08-12: fixed typo in preference plist filename.</em>