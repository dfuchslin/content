---
id: 479
title: 'Connecting to a remote dockerd on RancherOS on FreeNAS'
date: '2018-12-13T21:56:15+01:00'
author: David
layout: revision
guid: 'https://david.gyttja.com/2018/12/13/467-revision-v1/'
permalink: '/?p=479'
---

<!-- wp:paragraph -->
<p>Last summer I migrated from an old QNAP NAS to FreeNAS. I also started using docker containers with RancherOS, via<a href="https://www.ixsystems.com/documentation/freenas/11/vms.html#docker-rancher-vm"> the FreeNAS-provided virtual machine image</a>. I initially installed all my containers using the RancherOS cli (ssh into the machine), but now I am moving my container configurations out of the VM cli and versioning them so that I can at any point rebuild (or upgrade) the RancherOS disk image, and be able to reload all my containers. So I want to be able to run docker commands from my localhost cli, but have them be run on the RancherOS docker daemon.</p>
<!-- /wp:paragraph -->

<!-- wp:more -->
<!--more-->
<!-- /wp:more -->

<!-- wp:heading {"level":4} -->
<h4>Expose docker in RancherOS</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>To simplify life, copy your public ssh key to the RancherOS VM to enable passwordless login:</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">ssh-copy-id rancher@&lt;rancheros-ip></pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>From MacOS with docker installed, run <a href="https://docs.docker.com/machine/drivers/generic/#example">the following command</a> to set up a docker connection from localhost to RancherOS, as well as magically open dockerd for TCP connections on port 2376 on the RancherOS VM:</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">docker-machine create \<br>  --driver generic \<br>  --generic-ip-address=&lt;rancheros-ip> \<br>  --generic-ssh-key ~/.ssh/id_rsa \<br>  --generic-ssh-user=rancher \<br>  rancheros-docker<br></pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>To verify the new machine configuration was created run the following:</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">$ docker-machine ls<br>NAME               ACTIVE   DRIVER    STATE     URL                         SWARM   DOCKER        ERRORS<br>rancheros-docker   *        generic   Running   tcp://&lt;rancheros-ip>:2376           v17.09.1-ce</pre>
<!-- /wp:preformatted -->

<!-- wp:heading {"level":4} -->
<h4>Use docker from RancherOS on localhost</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Switch the docker context to the newly created docker machine configuration:</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">eval $(docker-machine env rancheros-docker)</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>Now, any docker commands will be executed on the RancherOS docker, not localhost.</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":4} -->
<h4>Reset docker on localhost back to defaults</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>To reset localhost back to normal (use the locally installed docker):</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">eval "$(docker-machine env -u)"</pre>
<!-- /wp:preformatted -->

<!-- wp:heading {"level":3} -->
<h3>Upcoming changes</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Starting with <a href="https://docs.docker.com/engine/release-notes/#1809">Docker 18.09</a>, connecting to a remote docker can be done much more easily as <a href="https://github.com/docker/cli/pull/1014">ssh connections are supported in the DOCKER_HOST variable</a>! So that means all of the above can be replaced by just a simple export:</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">export DOCKER_HOST=ssh://rancher@&lt;rancheros-ip></pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>This will require Docker 18.09 both locally and on the RancherOS VM, but it looks it will be a while before both RancherOS and FreeNAS have 18.09 available. The latest stable FreeNAS 11.2 has RancherOS 1.4.2, which has Docker 18.06, perhaps FreeNAS 11.3?</p>
<!-- /wp:paragraph -->