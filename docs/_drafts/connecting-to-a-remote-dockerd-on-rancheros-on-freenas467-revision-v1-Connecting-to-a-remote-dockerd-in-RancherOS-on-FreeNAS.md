---
id: 469
title: 'Connecting to a remote dockerd in RancherOS on FreeNAS'
date: '2018-12-13T20:52:10+01:00'
author: David
layout: revision
guid: 'https://david.gyttja.com/2018/12/13/467-revision-v1/'
permalink: '/?p=469'
---

<!-- wp:paragraph -->
<p>Last summer I migrated from an old QNAP NAS to FreeNAS. I also started using docker containers using RancherOS, using<a href="https://www.ixsystems.com/documentation/freenas/11/vms.html#docker-rancher-vm"> the FreeNAS-provided virtual machine image</a>. I initially installed all my containers using the RancherOS cli (ssh into the machine), but now I am moving my container configuration out and versioning it so that I can at any point rebuild (or upgrade) the RancherOS disk image, and be able to reload all my containers.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph -->