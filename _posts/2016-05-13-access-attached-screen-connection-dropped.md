---
id: 193
title: Access to (Attached) screen after connection dropped
date: 2016-05-13T15:03:08+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=193
permalink: /access-attached-screen-connection-dropped/
categories:
  - linux
---
Sometimes, when you try to re-attach to screen session you are told that the screen session is still attached. Mostly happen when we are working in a flaky Internet connection.
<blockquote>root@vagrant:~# screen -ls
There is a screen on:
20105.pts-3.vagrant    (05/13/2016 12:48:56 PM)    (Attached)
1 Socket in /var/run/screen/S-root.</blockquote>
&nbsp;

Here is a simple workaround way to take back that screen session.
<pre>screen -D -r '20105.pts-3.vagrant'</pre>
<!--more-->
Here is a <del datetime="2012-03-01T12:37:17+00:00"></del><strong>longer way</strong> to remove the process that is attached to that screen session, so you can reattach to it and continue with whatever you were doing.
<ul>
 	<li>Figure out which tty is holding on to the screen session by typing into terminal
<code>ps -ef | grep screen | grep tty</code></li>
 	<li>You will get something like this
<code>user 5730 5688 0 12:31 ttyp1 00:00:00 screen -r 20105.pts-3.vagrant</code></li>
 	<li>In this case the tty is 5688, use this to find the login bash that is associated with that tty
<code>ps -ef | grep bash | grep 5688</code></li>
 	<li>result of this should be something like
<code>user 5688 5687 0 12:28 ttyp1 00:00:00 -bash</code></li>
 	<li>kill process
<code>kill -9 5687</code></li>
</ul>
Now you should be free to re-attach to this screen session.