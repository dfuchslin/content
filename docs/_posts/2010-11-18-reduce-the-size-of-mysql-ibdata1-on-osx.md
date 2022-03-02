---
id: 50
title: 'Reduce the size of MySQL ibdata1 on OSX'
date: '2010-11-18T17:59:35+01:00'
author: David
layout: post
guid: 'http://gyttja.wordpress.com/?p=50'
permalink: /2010/11/18/reduce-the-size-of-mysql-ibdata1-on-osx/
categories:
    - OSX
    - Ubuntu
---

So I finally figured out why my TimeMachine backups were becoming bigger and bigger, 3-4GB backing up every day when I come home from work... It seems that my MySQL database file (<code>/usr/local/mysql/data/ibdata1</code>) keeps getting larger and larger, unnecessarily, even if I deleted databases, tables, etc. It seems even if I only update a few rows in a table in a small database, the ginormous <code>idbdata1</code> file grows and then get marks as a candidate for backup into TimeMachine. Ugh.

<!--more-->

I did some digging and found this interesting tutorial on <a title="Howto: Clean a mysql InnoDB storage engine?" href="http://stackoverflow.com/questions/3927690/howto-clean-a-mysql-innodb-storage-engine" target="_blank">how to clean up InnoDB storage files</a>.  Here I'll explain what I specifically did on my OSX 10.6.5 machine with MySQL v5.1.38.
<ol>
	<li>If you're not a cowboy, stop MySQL, backup all files, then start MySQL again (I used the System pref to stop/start MySQL, feel free to use the command-line instead):
<img src="https://david.gyttja.com/wp-content/uploads/2010/11/mysql-stop-pref.png" alt="" title="mysql-stop-pref" width="630" height="150" class="alignnone size-full wp-image-85" />
<pre language="bash">
$ sudo cp -R /usr/local/mysql/data /usr/local/mysql/data.bak
</pre>
<img src="https://david.gyttja.com/wp-content/uploads/2010/11/mysql-start-pref.png" alt="" title="mysql-start-pref" width="630" height="150" class="alignnone size-full wp-image-84" />
	</li>
	<li>Export all data from MySQL:
<pre language="bash">
$ mysqldump -u root -p --all-databases &gt; alldatabases.sql
</pre>
	</li>
	<li>Drop databases in MySQL (except "mysql" and "information_schema"):
<pre language="bash">
$ mysql -u root -p
mysql&gt; show databases;
mysql&gt; drop database XXXX;
</pre>
or use <a title="Drop All Databases in MySQL" href="http://rootedlabs.wordpress.com/2009/10/03/drop-all-databases-in-mysql/" target="_blank">this great one-liner to delete all databases</a>, modded a bit so it would work for me:
<pre language="bash">
# measure twice, cut once... make sure we are deleting what we should be deleting
$ mysql -u root -p  -e &quot;show databases&quot; | grep -v Database | grep -v mysql | grep -v information_schema | awk '{print &quot;drop database &quot; $1 &quot;;select sleep(0.1);&quot;}'
# now delete them
$ mysql -u root -p  -e &quot;show databases&quot; | grep -v Database | grep -v mysql | grep -v information_schema | awk '{print &quot;drop database &quot; $1 &quot;;select sleep(0.1);&quot;}' | mysql -uroot -ppassword
</pre>
	</li>
	<li>Stop MySQL again</li>
	<li>Add the following to the <code>[mysqld]</code> section in <code>/etc/my.cnf</code>:
<pre language="bash">
[mysqld]
innodb_file_per_table
</pre>
	</li>
	<li>Remove the files:
<pre language="bash">
$ sudo rm /usr/local/mysql/data/ibdata1
$ sudo rm /usr/local/mysql/data/ib_logfile0
$ sudo rm /usr/local/mysql/data/ib_logfile1
</pre>
	</li>
	<li>Start MySQL again</li>
	<li>Reload the databases from the sql dump-file:
<pre language="bash">
$ mysql -u root -p &lt; alldatabases.sql
</pre>
	</li>
	<li>Verify that your database(s) are working properly</li>
	<li>Delete the backup
<pre language="bash">
$ sudo rm -rf /usr/local/mysql/data.bak
</pre>
	</li>
	<li>Done!</li>
</ol>

After this modification my TimeMachine backups are much more reasonably-sized. Very nice!

Furthermore, I noticed the same infamously large <code>ibdata1</code> file on our continuous-integration build server at work: it was 40GB! I applied the same modifications as above. That server runs Ubuntu 10.10, the MySQL files are located in <code>/var/lib/mysql/data</code>, but otherwise the steps are pretty much the same. Even build-server bongo seems snappier now, and the total size of the MySQL data folder is 1GB (insane that there was 39GB of "dead" data in the ibdata1-file...)

Very happy.