---
id: 145
title: 'No more annoying password popups for Cisco VPN on OSX Lion (and Mountain Lion)!'
date: '2012-06-11T17:51:07+02:00'
author: David
layout: post
guid: 'http://gyttja.wordpress.com/?p=145'
permalink: /2012/06/11/no-more-annoying-password-popups-for-cisco-vpn-on-osx-lion/
categories:
    - OSX
---

I am currently working on a development project for our office in München. Accessing their internal servers requires connection via VPN (I'm working from Stockholm). I'm using the very handy built-in Cisco IPSEC VPN client in OSX and have had some annoying problems which until today I have not been able to solve. I am documenting these configuration changes so I remember what I did, and hopefully it can also help others out there!

<!--more-->

<h3>The problem</h3>
After being connected via VPN for about 48-54 minutes (seems to vary), OSX will throw up a "please enter password" dialog (I can't remember the exact wording...). After entering the password, the VPN connection stays active for another 48-54 minutes, at which time another password dialog pops up. Lather, rinse, repeat. Not very fun during a standard work day, especially when my application-in-progress likes to crap out as soon as it loses connectivity to those remote servers (and requires lengthy restarts).
<h3>The solution (I thought)</h3>
After much googling I found <a title="Save Password of Cisco VPN in Mac OS X 10.6" href="http://simon.heimlicher.com/articles/2011/03/17/cisco-vpn" target="_blank">this solution</a>, for which I had high hopes (despite the comments from fellow OSX Lion users who couldn't get the solution to work). In short, that post goes about showing how to grant <code>/usr/libexec/configd</code> access to your keychain, in order to squelch the password dialog. Well, unfortunately that solution didn't work for me as well :(
<h3>The working solution (finally!)</h3>
After a week or so of still getting that annoying password dialog, I managed to google the correct sequence of terms and I finally found a working solution! Over at the <a title="Built-in IPsec VPN randomly drops to Cisco VPN server" href="https://discussions.apple.com/message/18164765#18164765" target="_blank">Apple forums</a>, a very clever Mr Geordiadis posts a working solution to the problem. His solution is to modify the <code>racoon</code> configuration files for the VPN connection by tweaking a few settings and increasing the negotiated password timeout value from 3600 seconds to 24 hours (perfectly fine for my intended use). I've been connected now for over 8 hours today, haven't had a password dialog yet! So excellent! Confirmed that it works on Mountain Lion (10.8) as well.

I hope this information helps you as it helped me!

<h3>Steps:</h3>
<ul>
<li>Connect to the VPN so the configuration file is generated</li>
<li>
Create a location for the VPN configuration files
<pre language="bash">
$ sudo mkdir /etc/racoon/vpn
</pre>
</li>
<li>
Copy the auto-generated configuration file into the new configuration folder:
<pre language="bash">
$ sudo cp /var/run/racoon/1.1.1.1.conf /etc/racoon/vpn/
</pre>
</li>
<li>
Edit the racoon.conf file:
<pre language="bash">
$ sudo emacs /etc/racoon/racoon.conf
</pre>
</li>
<li>
Comment out the include line at the end of the file and include the new configuration folder:
<pre language="bash">
#include &quot;/var/run/racoon/*.conf&quot; ;
include &quot;/etc/racoon/vpn/*.conf&quot; ;
</pre>
</li>
<li>
Edit the VPN configuration file:
<pre language="bash">
$ sudo emacs /etc/racoon/vpn/1.1.1.1.conf
</pre>
<ul>
<li>
Disable dead peer detection:
<pre language="bash">
dpd_delay 0;
</pre>
</li>
<li>
Change proposal check to claim from obey:
<pre language="bash">
proposal_check claim;
</pre>
</li>
<li>
Change the proposed lifetime in each proposal (24 hours instead of 3600 seconds):
<pre language="bash">
lifetime time 24 hours;
</pre>
</li>
</ul>
</li>
<li>
Disconnect VPN and reconnect.
</li>
</ul>

<em>Updated 2012-08-07: added the detailed steps</em>
<em>Updated 2012-08-07: tested on OSX Mountain Lion (10.8)</em>