---
id: 174
title: How to Create Empty File with Specified Size on Linux
date: 2016-05-08T13:00:08+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=174
permalink: /create-empty-file-specified-size-linux/
categories:
  - Uncategorized
---
If you require to do some upload/download test, you can create a dummy file which have specific file size. The following commands create a 20MB file called <code>foo</code> .:
<ol>
 	<li><code>fallocate</code>
<pre><code>fallocate -l 20000000 foo
</code></pre>
This command is particularly impressive since it is as fast as <code>truncate</code> (instantaneous) irrespective of the desired file size (unlike the other solutions which will be slow for large files) and yet creates normal files, not sparse ones :
<pre><code>$ truncate -s 20MB foo1.txt 
$ fallocate -l 20000000 foo2.txt 
$ ls -ls foo?.txt 
    0 -rw-r--r-- 1 root root 14000000 Jun 21 03:54 foo1.txt
13672 -rw-r--r-- 1 root root 14000000 Jun 21 03:55 foo2.txt
</code></pre>
</li>
 	<li>Create a file filled with random data
<pre><code>dd if=/dev/urandom of=foo bs=1024k count=20
</code></pre>
</li>
 	<li>Create a file filled with <code>\0</code>s:
<pre><code>dd if=/dev/zero of=foo.txt bs=1024k count=20
</code></pre>
</li>
 	<li>Create a file filled with the first 20MB of data of another file:
<pre><code>head -c 20MB bar.txt &gt; foo
</code></pre>
</li>
 	<li>Create a file filled with the last 20MB of data of another file:
<pre><code>tail -c 20MB bar.txt &gt; foo
</code></pre>
&nbsp;</li>
</ol>