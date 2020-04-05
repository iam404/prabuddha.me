---
id: 543
title: 'How to Fix &#8220;E: Failed to fetch &#8230; Hash Sum mismatch&#8221;'
date: 2016-10-16T11:01:43+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=543
permalink: /fix-e-failed-fetch-hash-sum-mismatch/
dsq_thread_id:
  - "6218592211"
categories:
  - linux
---
To get rid of this error, open a terminal window and fire up the below commands :.
<pre>sudo rm -rf /var/lib/apt/lists/*
sudo apt-get clean
sudo apt-get update</pre>