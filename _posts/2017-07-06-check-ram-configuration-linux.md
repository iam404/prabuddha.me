---
id: 433
title: Check Ram Configuration From Linux
date: 2017-07-06T08:25:12+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=433
permalink: /check-ram-configuration-linux/
dsq_thread_id:
  - "6378152922"
categories:
  - linux
---
<ol>
 	<li>Open the terminal app or log in using ssh.</li>
</ol>
<blockquote>sudo dmidecode --type 17</blockquote>
&nbsp;

OR

You can use the following command too:
<blockquote>sudo lshw -short -C memory</blockquote>