---
id: 673
title: '(debian stretch) ps: command not found'
date: 2017-04-24T23:50:41+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=673
permalink: /debian-stretch-ps-command-not-found/
categories:
  - DevOps
---
Error:
<pre class="gh-header-title"><span class="js-issue-title">ps: command not found</span></pre>
Fix:
<pre>apt-get update &amp;&amp; apt-get install -y procps</pre>
&nbsp;