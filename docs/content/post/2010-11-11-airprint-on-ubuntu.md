---
author: David
categories:
  - iOS
  - Ubuntu
date: '2010-11-11T22:12:49+01:00'
guid: http://gyttja.wordpress.com/?p=3
id: 6
permalink: /2010/11/11/airprint-on-ubuntu/
tags:
  - AirPrint
title: AirPrint on Ubuntu
url: /2010/11/11/airprint-on-ubuntu/
---


<h2>How to print from iPhone/iPad (iOS 4.2) via Ubuntu</h2>
I just noticed that with the latest 10.6.5 update, Apple at the last minute <a href="http://www.macrumors.com/2010/11/10/steve-jobs-replies-regarding-rumors-of-airprint-issues/" target="_blank">disabled support for printing from iOS 4.2 devices to shared printers in OSX</a>. Instead of trying to <a href="http://blog.steventroughtonsmith.com/2010/11/return-airprint-sharing-to-mac-os-x.html" target="_blank">overwrite new drivers with prerelease versions</a>, I thought that there must be someone, somewhere out there who has figured out how to do this using the fine, free tools available to us. I like open standards, open tools, open source.

<!--more-->

From what I understand AirPrint supports printing two ways:
<ul>
	<li>Via officially-supported HP ePrint printers that advertise themselves on your subnet</li>
	<li>Via shared printers from Mac/Windows (this is the part that apparently got axed, at least in the latest 10.6.5 update... don't know how Windows will fare)</li>
</ul>
I don't have a new HP ePrint printer, but I do have a wonderful Ubuntu 10.04 server running avahi (open source Bonjour/mDNS responder)... I wonder if there's a way to have my server act as an AirPrint device and then send the print job to my networked printer?

Enter <a href="http://www.rho.cc/index.php/linux2/48-misc/104-printing-from-ipad-airprint-via-cups" target="_blank">this post</a>. Excellent! Set up a Bonjour service, point it to a shared printer, done!

Here's what I had to do in Ubuntu  to get printing to work, YMMV:
<ul>
	<li>Install my printer (networked HP Color LaserJet 1515n) using the graphical configuration utility (System-&gt;Administration-&gt;Printing). Make sure the printer is shared.</li>
	<li>Updated my /etc/cups/cupsd.conf configuration to allow network access (default Ubuntu configuration is for localhost only):
<pre language="bash">
# Only listen for connections from the local machine.
#Listen localhost:631
#Listen /var/run/cups/cups.sock
Port 631
ServerAlias *
</bash>

and

<pre language="bash">
# Restrict access to the server...
&lt;Location /&gt;
Order allow,deny
Allow @LOCAL
&lt;/Location&gt;
</pre></li>
	<li>Tested remote access to Ubuntu's cups web GUI from my laptop (to make sure machines other than the Ubuntu server had access to cups): http://172.16.0.50:631/printers/</li>
	<li>Tested that I could print remotely from my laptop to the new shared printer on Ubuntu (simple "Add Printer" in OSX and then print test page)</li>
	<li>Configured avahi with a new printer service /etc/avahi/services/printer.service. As mentioned in the original link, rp and adminurl are the most important configuration bits.
<pre language="bash">
&lt;?xml version=&quot;1.0&quot; standalone='no'?&gt;&lt;!--*-nxml-*--&gt;
&lt;!DOCTYPE service-group SYSTEM &quot;avahi-service.dtd&quot;&gt;
&lt;service-group&gt;
  &lt;name&gt;HP 1515n&lt;/name&gt;
  &lt;service&gt;
    &lt;type&gt;_ipp._tcp&lt;/type&gt;
    &lt;subtype&gt;_universal._sub._ipp._tcp&lt;/subtype&gt;
    &lt;port&gt;631&lt;/port&gt;
    &lt;txt-record&gt;txtver=1&lt;/txt-record&gt;
    &lt;txt-record&gt;qtotal=1&lt;/txt-record&gt;
    &lt;txt-record&gt;rp=printers/hp-cp1515n&lt;/txt-record&gt;
    &lt;txt-record&gt;ty=HP 1515n&lt;/txt-record&gt;
    &lt;txt-record&gt;adminurl=http://172.16.0.50:631/printers/hp-cp1515n&lt;/txt-record&gt;
    &lt;txt-record&gt;note=HP Color LaserJet cp1515n&lt;/txt-record&gt;
    &lt;txt-record&gt;priority=0&lt;/txt-record&gt;
    &lt;txt-record&gt;product=virtual Printer&lt;/txt-record&gt;
    &lt;txt-record&gt;printer-state=3&lt;/txt-record&gt;
    &lt;txt-record&gt;printer-type=0x801046&lt;/txt-record&gt;
    &lt;txt-record&gt;Transparent=T&lt;/txt-record&gt;
    &lt;txt-record&gt;Binary=T&lt;/txt-record&gt;
    &lt;txt-record&gt;Fax=F&lt;/txt-record&gt;
    &lt;txt-record&gt;Color=T&lt;/txt-record&gt;
    &lt;txt-record&gt;Duplex=T&lt;/txt-record&gt;
    &lt;txt-record&gt;Staple=F&lt;/txt-record&gt;
    &lt;txt-record&gt;Copies=T&lt;/txt-record&gt;
    &lt;txt-record&gt;Collate=F&lt;/txt-record&gt;
    &lt;txt-record&gt;Punch=F&lt;/txt-record&gt;
    &lt;txt-record&gt;Bind=F&lt;/txt-record&gt;
    &lt;txt-record&gt;Sort=F&lt;/txt-record&gt;
    &lt;txt-record&gt;Scan=F&lt;/txt-record&gt;
    &lt;txt-record&gt;pdl=application/octet-stream,application/pdf,application/postscript,image/jpeg,image/png,image/urf&lt;/txt-record&gt;
    &lt;txt-record&gt;URF=W8,SRGB24,CP1,RS600&lt;/txt-record&gt;
  &lt;/service&gt;
&lt;/service-group&gt;
</pre></li>
	<li>Printed a page from iOS simulator
<a href="/images/2010/11/print-dn.png"><img class="alignnone size-full wp-image-9" title="AirPrinting DN från iOS simulator" src="/images/2010/11/print-dn.png" alt="" width="630" height="816" /></a></li>
	<li>Printed a page from iPhone 3GS with iOS 4.2.1:
<img class="alignnone size-medium wp-image-93" title="iphone-airprint" src="/images/2010/11/iphone-airprint.png?w=200" alt="" width="200" height="300" /></li>
	<li>Done!</li>
</ul>
I'm not sure if all printers will work out of the box with this configuration, but since my printer supports PostScript I assume it can rasterize pretty much anything iOS will send it. In any case, I didn't have to configure any filters or print settings. It just worked. Hopefully Apple won't further cripple AirPrinting by also "patching" iOS so that only HP ePrint devices are supported and it no longer recognizes Bonjour services with subtype <span style="font-family:Consolas, Monaco, 'Courier New', Courier, monospace;line-height:18px;font-size:12px;white-space:pre;">_universal._sub._ipp._tcp</span>. We'll see what happens!

Now all I need is an iPad. And a reason to print.

<em>Updated 2010-11-21: repaired some mangled XML due to Wordpress's less-than-nice HTML-parsing</em>

<em>Updated 2010-11-23: added post subtitle, added screenshot from iPhone</em>
