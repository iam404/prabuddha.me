---
id: 644
title: Count number of thread in a linux process
date: 2018-01-25T09:13:24+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=644
permalink: /count-number-thread-linux-process/
dsq_thread_id:
  - "6483947074"
categories:
  - DevOps
---
There are multiple way for finding out the number of thread spawned by a process. Simplest or one of the way if to find from the /proc status of the process.

The <tt>proc</tt> pseudo filesystem, which resides in /proc directory, is the easiest way to see the thread count of any active process. The /proc directory exports in the form of readable text files a wealth of information related to existing processes and system hardware such as CPU, interrupts, memory, disk, etc.
<pre class="console">$ cat /proc/&lt;pid&gt;/status</pre>
<div></div>
The above command will show detailed information about the process with &lt;pid&gt;, which includes process state (e.g., sleeping, running), parent PID, UID, GID, the number of file descriptors used, and the number of context switches. The output also indicates <strong>the total number of threads created in a process</strong> as follows.

<strong>Example:</strong>
<pre>root@aws:~# cat /proc/28342/status | grep Thread
Threads: 1</pre>
<strong>Other methods:</strong>
<pre class="p1"><span class="s1">ps -T -p &lt;pid of the process&gt;</span></pre>