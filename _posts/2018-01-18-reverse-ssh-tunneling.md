---
id: 606
title: 'How To: Reverse SSH Tunneling'
date: 2018-01-18T19:17:17+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=606
permalink: /reverse-ssh-tunneling/
dsq_thread_id:
  - "6422621256"
categories:
  - DevOps
  - linux
tags:
  - ssh
---
In order to SSH into a machine behind a firewall, or access a service running on that system, you will need to use Reverse SSH Port Forwarding. The machine in question needs to open an SSH connection to the outside world and include a <em>-R</em> tunnel whose entry point is the remote side (from server in our example) to connect to your machine, allocate a port there and make certain that any connection request on that port is then forwarded to the SSH port of the remote side (server).
<pre>ssh –f –N –T -R 1333:localhost:22 user@your.server</pre>
<strong>Flag:</strong>
<ul>
 	<li><strong>-f:</strong> tells the SSH to background itself after it authenticates, saving you time by not having to run something on the remote server for the tunnel to remain alive.</li>
 	<li><strong>-N:</strong> if all you need is to create a tunnel without running any remote commands then include this option to save resources.</li>
 	<li><strong>-T:</strong> useful to disable pseudo-tty allocation, which is fitting if you are not trying to create an interactive shell.</li>
</ul>
That will open the port 1333 on <em>your.server</em> .

All packets arriving at this port are transferred through the SSH tunnel to your home PC.

<strong>Now you need to do an SSH connection request from your machine to your own machine at port 1333:</strong>
<pre>ssh -p 1333 username@localhost</pre>
&nbsp;

<strong>Note:</strong>
<ul>
 	<li>By default the opened port is just bound to <code class="highlighter-rouge">127.0.0.1</code> (the servers <a href="http://en.wikipedia.org/wiki/Loopback">loopback</a> interface), so you can only send packets from the server itself (or need some more network hacking). To have this hack listening to <code class="highlighter-rouge">0.0.0.0</code> (all interfaces) add the following to your <code class="highlighter-rouge">/etc/ssh/sshd_config</code> on <code class="highlighter-rouge">your.server</code> and restart the daemon :</li>
</ul>
<figure class="highlight">
<pre><code class="language-bash" data-lang="bash">GatewayPorts yes</code></pre>
&nbsp;</figure>
<ul>
 	<li>Run something like screen or top on the server to always transfer packets (otherwise the connection will be closed after some time), with <code class="highlighter-rouge">-o ServerAliveInterval=XXX</code> you can adjust the threshold for closing the SSH connection. Surround it with a <code class="highlighter-rouge">while</code> loop and you’ll reestablish closed connections (network errors or something like that):
<figure class="highlight">
<pre><code class="language-bash" data-lang="bash"><span class="k">while</span> <span class="o">[</span> 1 <span class="o">]</span><span class="p">;</span> <span class="k">do </span>ssh <span class="nt">-o</span> <span class="nv">ServerAliveInterval</span><span class="o">=</span>60 <span class="nt">-R</span> 1333:localhost:22 user@your.server<span class="p">;</span> <span class="k">done</span></code></pre>
</figure>
</li>
</ul>
&nbsp;