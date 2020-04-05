---
id: 240
title: Increase tmpfs partition without restarting
date: 2016-03-27T02:35:29+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=240
permalink: /increase-tmpfs-partition-without-restarting/
categories:
  - linux
---
Low on space in <strong>/run</strong>.

tmpfs filesystem keeps the entire filesystem (with all it’s files) in virtual memory. All data is stored in memory, which means the data is temporary and will be lost after a reboot. Typically a <b>tmpfs</b> filesystem mounted at <u><a href="file:/dev/shm">/dev/shm</a></u> as used for the implementation of POSIX shared memory.

The filesystem is automatically created when mounting a filesystem with the type <b>tmpfs</b> via a command such as the following:
<pre>$ sudo mount -t tmpfs -o size=10M tmpfs /mnt/mytmpfs</pre>
<h6>To increase the size, do the following:</h6>
<ol>
 	<li>Modify <strong>/etc/fstab</strong> line to look something like this:
<pre>tmpfs                /run                    tmpfs   defaults,size=10g       0 0

#OR 

tmpfs                /dev/shm                tmpfs   defaults,size=10g        0 0</pre>
</li>
 	<li>
<pre>mount -o remount,size=10G /run

#OR

mount -o remount /dev/shm</pre>
</li>
 	<li>
<pre>df -h      (to see the changes)</pre>
</li>
 	<li><em>Note: Be careful not too increase it too much b/c the system will deadlock since the OOM (Out-Of-Memory) handler can not free up that space.</em></li>
</ol>
&nbsp;