---
id: 390
title: 'Generate dependency graph for Hybris'
date: '2015-05-18T22:42:01+02:00'
author: David
layout: post
guid: 'https://david.gyttja.com/?p=390'
permalink: /2015/05/18/generate-dependency-graph-for-hybris/
categories:
    - Hybris
---

I'm currently working as a developer on a large <a href="https://www.hybris.com/en/" target="_blank">Hybris</a> e-commerce solution. The codebase has been delivered to us with a large number of extensions and I was having a hard time visualizing how all the extensions were interconnected. I put together this simple script one evening, and made some quick tweaks at work to get it to work with our codebase. After generating the graph, we could immediately see where we were having dependency issues and were able to make adjustments.

<!--more-->

Anyway, as the Hybris community is such a closed community I thought I'd share this utility. Perhaps it will also be useful for some of you Hybris developers out there!

The script: <a href="https://github.com/dfuchslin/hybris-tools/tree/master/dependency-graph" target="_blank">https://github.com/dfuchslin/hybris-tools/tree/master/dependency-graph</a>

Sample dependency graph output:
<a href="https://david.gyttja.com/wp-content/uploads/2015/05/extensions.svg"><img src="https://david.gyttja.com/wp-content/uploads/2015/05/extensions.svg" alt="extensions" width="830" height="418" class="aligncenter size-medium wp-image-392" /></a>