---
id: 426
title: 'Crashplan eating up TimeMachine backups'
date: '2016-03-13T20:13:35+01:00'
author: David
layout: revision
guid: 'https://david.gyttja.com/2016/03/13/399-revision-v1/'
permalink: '/?p=426'
---

I recently upgraded to El Capitan and while checking the system logs I noticed lots of errors about not being able to delete old backups (thin backups), such as:
<code>
2016-01-10 05:22:48,193 com.apple.backupd[5904]: Starting post-backup thinning
2016-01-10 05:22:49,319 com.apple.backupd[5904]: Error: Error Domain=NSOSStatusErrorDomain Code=-36 "ioErr: I/O error (bummers)" deleting backup: /Volumes/Time Machine Backups/Backups.backupdb/fünke/2016-01-01-213437
...
</code>
and

<!--more-->

<code>
2016-01-10 05:22:45,791 com.apple.mtmd[208]: Failed to remove /.MobileBackups.trash, error: Error Domain=NSCocoaErrorDomain Code=513 "“.MobileBackups.trash” couldn’t be removed because you don’t have permission to access it." UserInfo={NSFilePath=/.MobileBackups.trash, NSUserStringVariant=(
Remove
), NSUnderlyingError=0x7f9f02c3baf0 {Error Domain=NSPOSIXErrorDomain Code=1 "Operation not permitted"}}
2016-01-10 05:22:45,792 com.apple.mtmd[208]: Failed to delete /.MobileBackups.trash, error: Error Domain=NSCocoaErrorDomain Code=513 "“.MobileBackups.trash” couldn’t be removed because you don’t have permission to access it." UserInfo={NSFilePath=/.MobileBackups.trash, NSUserStringVariant=(
Remove
), NSUnderlyingError=0x7f9f02c3baf0 {Error Domain=NSPOSIXErrorDomain Code=1 "Operation not permitted"}}
</code>

These seemed a bit disconcerting: TimeMachine was having issues thinning old backups on my TimeCapsule, and worse yet, TimeMachine couldn't thin the MobileBackups on my MacBook Pro, meaning my already size-challenged SSD was being filled up by backups that would never be deleted. After lots and lots of googling, I finally found <a href="https://discussions.apple.com/message/29277434#29277434" target="_blank">this post which has fixed the problem</a>!

It seems when you install CrashPlan, it sets file system attributes that do not allow itself to get deleted. So with a bit of command-line wizardry, we can fix this problem. We need to remove the attributes from all instances of CrashPlan.app in all backed-up locations.

Fix the MobileBackups:
<ol>
	<li><code>sudo su root</code> to change to the root user (be careful!)</li>
	<li><code>find /.MobileBackups -type d -iname crashplan.app</code> to look for all backed-up instances of CrashPlan.app</li>
	<li>For each found instance, do this: <code>/System/Library/Extensions/TMSafetyNet.kext/Contents/Helpers/bypass chflags noschg,nouchg path/to/CrashPlan.app</code> replace path/to/CrashPlan.app to the path found in #2</li>
	<li><code>exit</code> to exit from the root account</li>
</ol>

Do the same for MobileBackups.trash:
<ol>
	<li><code>sudo su root</code> to change to the root user (be careful!)</li>
	<li><code>find /.MobileBackups.trash -type d -iname crashplan.app</code> to look for all backed-up instances of CrashPlan.app</li>
	<li>For each found instance, do this: <code>/System/Library/Extensions/TMSafetyNet.kext/Contents/Helpers/bypass chflags noschg,nouchg path/to/CrashPlan.app</code> replace path/to/CrashPlan.app to the path found in #2</li>
	<li><code>exit</code> to exit from the root account</li>
</ol>

Now, mount the TimeMachine backup disk and sparsebundle, and fix the attributes on all the backups there:
<ol>
	<li><code>sudo su root</code> to change to the root user (be careful!)</li>
	<li><code>cd /Volumes/Time\ Machine\ Backups/Backups.backupdb/{machine name}/</code></li>
	<li>Since I had so many backups, I wrote a quick one-liner script: <code>for dir in `ls`; do /System/Library/Extensions/TMSafetyNet.kext/Contents/Helpers/bypass chflags noschg,nouchg "${dir}/Macintosh HD/Applications/CrashPlan.app"; done</code></li>
	<li><code>exit</code> to exit from the root account</li>
</ol>

And finally, add /Applications/CrashPlan.app to the excluded list for TimeMachine backups so that it is not backed up again.

Done!

After I did the above, I triggered a new TimeMachine backup, checked the logs, no more errors! And I checked my hard drive: I had now gained 18GB of space! TimeMachine was finally able to delete/trim the old unused Mobile Backups! :)

Update 2016-02-03: changed the find script to case-insensitive <code>-iname crashplan.app</code>.