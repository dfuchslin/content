---
id: 384
title: 'Disable CrashPlanService on OSX'
date: '2015-05-01T11:01:40+02:00'
author: David
layout: post
guid: 'https://david.gyttja.com/?p=384'
permalink: /2015/05/01/disable-crashplanservice-on-osx/
categories:
    - OSX
    - QNAP
tags:
    - CrashPlan
---

Since I run <a href="/2011/10/09/crashplan-on-qnap/">CrashPlan on my QNAP</a> to back up my personal data, yet I still want to use the CrashPlan GUI on my Mac, I don't need the CrashPlanService running in the background on my Mac all the time.

<!--more-->

To disable the always-running CrashPlan service on OSX (verified with the latest CrashPlan app, version 3.7):
<ol>
<li>
See if the service is running:<code>
ps aux | grep CrashPlanService
</code>
</li>
<li>
Disable service:<code>
sudo launchctl unload /Library/LaunchDaemons/com.crashplan.engine.plist
</code>
</li>
<li>
Verify the service is not running anymore:<code>
ps aux | grep CrashPlanService
</code>
</li>
<li>
Delete the launch control plist so the CrashPlanService doesn't reload the next time OSX is restarted:<code>
sudo rm /Library/LaunchDaemons/com.crashplan.engine.plist
</code>
</li>
</ol>

That's it!