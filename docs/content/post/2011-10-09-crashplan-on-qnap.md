---
author: David
categories:
  - QNAP
date: '2011-10-09T21:09:41+02:00'
guid: http://gyttja.wordpress.com/?p=111
id: 111
permalink: /2011/10/09/crashplan-on-qnap/
tags:
  - backup
  - cloud
  - CrashPlan
title: CrashPlan on QNAP
url: /2011/10/09/crashplan-on-qnap/
---


First up on my <a title="Auf Wiedersehen Frankenstein, hello QNAP" href="https://david.gyttja.com/2011/10/08/auf-wiedersehen-frankenstein-hello-qnap/">QNAP</a>: offsite backup of my data in "the cloud". QNAP's recommended and integrated cloud backup provider was a bit too expensive for my taste, especially since I plan to backup nearly 1TB of data. After some serious googling I found the perfect candidate: <a title="CrashPlan" href="http://www.crashplan.com/" target="_blank">CrashPlan</a>.

<!--more-->

CrashPlan's software–written in Java–has two components, a server and a GUI client. This makes it a perfect candidate for QNAP. Get the server running in headless mode on my QNAP, then connect to it via the GUI client from my desktop machine.
<ul>
	<li>First up with the QNAP, install <a title="Install Optware IPKG" href="http://wiki.qnap.com/wiki/Install_Optware_IPKG" target="_blank">Optware IPKG</a>.</li>
	<li>Then follow Cokeman's excellent guide for <a title="CrashPlan QPKG" href="http://cokeman.dk/qnap/crashplan" target="_blank">CrashPlan on a QNAP</a>.</li>
	<li>Installed <a title="CrashPlan OSX" href="http://www.crashplan.com/consumer/download.html?os=Mac" target="_blank">CrashPlan for OSX</a> and modded the <code>ui.properties</code> file to <code>servicePort=4200</code> as in <a title="Connect to headless engine" href="http://support.crashplan.com/doku.php/how_to/configure_a_headless_client" target="_blank">CrashPlan's docs</a> (to edit I used TextMate):
<pre language="bash">
$ mate /Applications/CrashPlan.app/Contents/Resources/Java/conf/ui.properties
</pre></li>
	<li>Added a tunnel to my QNAP in <a title="SSHKeyChain" href="http://sshkeychain.sourceforge.net/" target="_blank">SSHKeyChain</a>(an excellent program to manage SSH connections and tunnels):

[caption id="attachment_117" align="alignnone" width="300"]<a href="/images/2011/10/crashplan-tunnel.png"><img class="size-medium wp-image-117" title="SSHKeyChain CrashPlan tunnel configuration" src="/images/2011/10/crashplan-tunnel.png?w=300" alt="SSHKeyChain CrashPlan tunnel configuration" width="300" height="292" /></a> SSHKeyChain CrashPlan tunnel configuration[/caption]</li>
	<li>Open CrashPlan on Mac. Because of the <code>ui.properties</code>configuration change, you will now be connecting from CrashPlan's Mac GUI to the CrashPlan engine on QNAP. Go through the CrashPlan setup, configure backup options, done!

[caption id="attachment_130" align="alignnone" width="300"]<a href="/images/2011/10/crashplan-remote.png"><img class="size-medium wp-image-130" title="Connecting to headless CrashPlan engine via OSX GUI" src="/images/2011/10/crashplan-remote.png?w=300" alt="Connecting to headless CrashPlan engine via OSX GUI" width="300" height="235" /></a> Connecting to headless CrashPlan engine via OSX GUI[/caption]</li>
</ul>
