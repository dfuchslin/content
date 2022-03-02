---
id: 208
title: 'Recover a failed TimeMachine backup'
date: '2013-01-08T23:11:18+01:00'
author: David
layout: post
guid: 'http://gyttja.wordpress.com/?p=208'
permalink: /2013/01/08/recover-a-failed-timemachine-backup/
geo_public:
    - '0'
categories:
    - OSX
tags:
    - TimeMachine
---

I recently received an unpleasant warning message after TimeMachine routinely tried to perform a backup:
<blockquote>Time Machine completed a verification of your backups on “matmos”. To improve reliability, Time Machine must create a new backup for you.
Click Start New Backup to create a new backup. This will remove your existing backup history. This could take several hours.
Click Back Up Later to be reminded tomorrow. Time Machine won’t perform backups during this time.<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-0-warning.png"><img class="size-medium wp-image-209" alt="Time Machine completed a verification of your backups on 'matmos'. To improve reliability, Time Machine must create a new backup for you.  Click Start New Backup to create a new backup. This will remove your existing backup history. This could take several hours.  Click Back Up Later to be reminded tomorrow. Time Machine won’t perform backups during this time." src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-0-warning.png?w=300" width="300" height="206" /></a></blockquote>

<!--more-->

Googling around for others with the same problem I found quite a few tips (like <a href="http://simon.heimlicher.com/articles/2009/01/10/time-machine-readonly" target="_blank">this one</a>, or <a href="http://www.garth.org/archives/2010,07,16,124,fixing-time-machine-sparsebundle-network-backup-errors.html" target="_blank">this one</a>). The basic idea is to mount the sparsebundle image, run a disk check/repair, and hope for the best. In my case (as you will see in a bit), my sparsebundle appeared to be hosed. My options: lose my old backups or look for a way to recover the old backups. But first up, turn off TimeMachine, and then try to run a standard disk check.
<h2>Run disk check/repair</h2>
<ul>
<li>
Unlock and mount the TimeMachine sparsebundle from the already-mounted server share (of course your server name, network share, sparsebundle names will not be the same as mine):
<pre language="bash">
$ sudo chflags nouchg /Volumes/TimeMachine-David/fünke.sparsebundle
$ sudo chflags nouchg /Volumes/TimeMachine-David/fünke.sparsebundle/token

$ sudo hdiutil attach -nomount -noverify -readwrite -noautofsck /Volumes/TimeMachine-David/fünke.sparsebundle
/dev/disk2          	GUID_partition_scheme          	
/dev/disk2s1        	EFI                            	
/dev/disk2s2        	Apple_HFS
</pre>
</li>
<li>
The disk check utility <code>fsck</code> may now be running, so get the PID of it and kill the process so we can manually run it:
<pre language="bash">
$ ps auxwww | grep fsck
$ kill PID
</pre>
</li>
<li>
Now run <code>fsck</code> with some repair options on the correct disk partition (use the "Apple_HFS" partition as listed in the mount step above (/dev/disk2s2 in my example):
<pre language="bash">
$ sudo fsck_hfs -dryf /dev/disk2s2
journal_replay(/dev/disk2s2) returned 0
** /dev/rdisk2s2
	Using cacheBlockSize=32K cacheTotalBlock=65536 cacheSize=2097152K.
   Executing fsck_hfs (version diskdev_cmds-557~393).
** Checking Journaled HFS Plus volume.
   Invalid number of allocation blocks
(4294967295, 0)
	IVChk - volume header total allocation blocks is greater than device size 
	volume allocation block count 102374400 device allocation block count 97630464 
** The volume could not be verified completely.
	volume check failed with error 7 
	volume type is pure HFS+ 
	primary MDB is at block 0 0x00 
	alternate MDB is at block 0 0x00 
	primary VHB is at block 2 0x02 
	alternate VHB is at block 781043710 0x2e8dc7fe 
	sector size = 512 0x200 
	VolumeObject flags = 0x07 
	total sectors for volume = 781043712 0x2e8dc800 
	total sectors for embedded volume = 0 0x00 
CheckHFS returned -1317, fsmodified = 1
</pre>
</li>
<li>
The disk check did not do anything, so let's unmount the sparsebundle:
<pre language="bash">
$ sudo hdiutil detach /dev/disk2s2
</pre>
</li>
<li>You can try running the disk check (fsck) multiple times. Some have reported that does the trick! In my case, it didn't help. I had to try something else.
</ul>
<h2>What to do next?</h2>
So, "The volume could not be verified completely" says that disk check is not going to repair my sparsebundle. But one good thing to note: the sparsebundle can be mounted read-only, so the old backups should still be there. So the plan: make a new sparsebundle, mount it, mount the old sparsebundle, and then copy all files from the old sparsebundle into the new. Sounds easy, right?
Making a new sparsebundle is not rocket science, however copying the files from TimeMachine backups can be quite challenging. I learned quite a bit when trying various methods to copy the files across (Finder, <code>cp</code>, <code>rsync</code>, <code>ditto</code>, etc). TimeMachine is quite ingenious: it uses a combination of file hard links and directory hard links (the latter is a new one to me!) in order to keep the backup size at a mininum. Unfortunately, all the methods I tried could not reconcile the directory hard links: instead of the links being created, the actual directory contents were copied. Furthermore, Apple has made it difficult to work directly with files in TimeMachine backups by making use of sandboxing and an access control "safety net" (see <a href="http://superuser.com/questions/162690/how-can-i-delete-time-machine-files-using-the-commandline" target="_blank">this</a> or <a href="http://techjournal.318.com/general-technology/the-time-machine-safety-net/#content" target="_blank">this</a>). So I did some more digging and found a great product that can deal with TimeMachine backups, directory hard links, and this safety net: <a href="http://www.shirt-pocket.com/SuperDuper/SuperDuperDescription.html" target="_blank">SuperDuper</a>.
<h2>Recover contents of failed sparsebundle</h2>
<h3>Move failed sparsebundle to a new location</h3>
I tried restoring the failed sparsebundle to a new sparsebundle while both were on a network drive (connected to my machine via gigabit ethernet) and the backup was painfully slow (wasn't finished after 24 hours) meaning the bottleneck was random access/seek time of my poor, slow network drives. I cancelled the restore operation, moved the failed sparsebundle to an external USB drive.
<h3>Create new sparsebundle</h3>
With the failed sparsebundle no longer in my TimeMachine network share, I created a new sparsebundle. Since I encrypt my backups, I used TimeMachine to create a new sparsebundle on the network share:
<ul>
<li>Enable TimeMachine, select the network share, select "encrypt backups", then "use disk"
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-1-selectdisk.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-1-selectdisk.png?w=300" alt="select network share in TimeMachine" width="300" height="214" class="alignnone size-medium wp-image-223" /></a>
</li>
<li>
Provide encryption password:
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-1-encrypt.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-1-encrypt.png?w=300" alt="create sparsebundle encryption key" width="300" height="214" class="alignnone size-medium wp-image-222" /></a>
</li>
<li>Let the backup run, then cancel after a few minutes. This will create a new sparsebundle on the network share.
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-1-cancel.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-1-cancel.png?w=300" alt="backing up..." width="300" height="119" class="alignnone size-medium wp-image-221" /></a>
</li>
</ul>
<h3>Mount both sparsebundles</h3>
<ul>
<li>Mount the failed sparsebundle (from the external/USB drive). Unfortunately, you can't use the paste command in the encryption password field :(
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-2-key.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-2-key.png?w=300" alt="field does not allow pasting!" width="300" height="196" class="alignnone size-medium wp-image-226" /></a>
</li>
<li>Note that the sparsebundle will be mounted read-only (which is just fine):
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-2-warning.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-2-warning.png?w=300" alt="Mac OSX can&#039;t repair the disk &#039;Time Machine Backups&#039;. You can still open or copy files on the disk, but you can&#039;t save changes to files on the disk. Back up the disk and reformat it as soon as you can." width="300" height="164" class="alignnone size-medium wp-image-227" /></a>
</li>
<li>
Now that both sparsebundles are mounted and have the same name (Time Machine Backups), we need to make sure we know which is the source (external drive) and which is the destination (network share). A little commandline magic:
<pre language="bash">
$ mount
...
/dev/disk4s2 on /Volumes/Time Machine Backups (hfs, local, nodev, nosuid, read-only, mounted by david)
/dev/disk5s2 on /Volumes/Time Machine Backups 1 (hfs, local, nodev, nosuid, journaled, mounted by david)
</pre>
</li>
So, "Time Machine Backups" is the source (it's read-only) and "Time Machine Backups 1" is the destination.
</ul>

<h3>Copy files from the failed sparsebundle to the new sparsebundle</h3>
<ul>
<li>
Open "SuperDuper!"
Select copy: "Time Machine Backups"
to: "Time Machine Backups 1"
using: "Backup - all files"
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd1.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd1.png?w=300" alt="SuperDuper" width="300" height="230" class="alignnone size-medium wp-image-232" /></a>
</li>
<li>
Select "Options..." and choose "Smart Update" (this prevents SuperDuper from reformatting the destination sparsebundle, ask me how i know ;) )
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd2.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd2.png?w=291" alt="Smart Update" width="291" height="300" class="alignnone size-medium wp-image-233" /></a>
</li>
<li>
Advanced options are left as the default:
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd3.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd3.png?w=291" alt="tm-3-sd3" width="291" height="300" class="alignnone size-medium wp-image-234" /></a>
</li>
<li>
Start the copy process:
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd4.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd4.png?w=300" alt="Start copy" width="300" height="230" class="alignnone size-medium wp-image-235" /></a>
</li>
<li>
Let it run for a long time...
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd5.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd5.png?w=300" alt="running" width="300" height="246" class="alignnone size-medium wp-image-236" /></a>
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd6.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-3-sd6.png?w=300" alt="done" width="300" height="240" class="alignnone size-medium wp-image-237" /></a>
</li>
</ul>
<h3>Enable TimeMachine</h3>
Enable TimeMachine and start the backups. When the backups first started, the "Oldest backup" date was not listed. But when the backup finished, TimeMachine successfully recognized the oldest backup. Success!
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-4-backup.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-4-backup.png?w=300" alt="Backing up" width="300" height="214" class="alignnone size-medium wp-image-238" /></a>
<a href="https://david.gyttja.com/wp-content/uploads/2013/01/tm-4-done.png"><img src="https://david.gyttja.com/wp-content/uploads/2013/01/tm-4-done.png?w=300" alt="done!" width="300" height="214" class="alignnone size-medium wp-image-240" /></a>

<em>Updated 2013-07-06: updated fsck step to not be recursive, can try to run fsck multiple times, thanks to comments!</em>