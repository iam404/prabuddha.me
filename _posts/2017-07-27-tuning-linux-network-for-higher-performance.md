---
id: 454
title: Tuning Linux Network for Higher Performance
date: 2017-07-27T22:21:30+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=454
permalink: /tuning-linux-network-for-higher-performance/
categories:
  - linux
---
<header class="sect-head">
<p class="lead entry-summary">Linux distributions still ship with the assumption that they will be multi-user systems, meaning resource limits are set for a normal human doing day-to-day desktop work. For a high-performance system trying to serve thousands of concurrent network clients, these limits are far too low. If you have an online game or web app that’s pushing the envelope, these settings can help increase awesomeness.</p>

</header>
<div class="entry-content">

The parameters we’ll adjust are as follows:
<ul>
 	<li>Increase max open files to 100,000 from the default (typically 1024). In Linux, every open network socket requires a file descriptor. Increasing this limit will ensure that lingering <code>TIME_WAIT</code> sockets and other consumers of file descriptors don’t impact our ability to handle lots of concurrent requests.</li>
 	<li>Decrease the time that sockets stay in the <code>TIME_WAIT</code> state by lowering <code>tcp_fin_timeout</code> from its default of 60 seconds to 10. You can lower this even further, but too low, and you can run into socket close errors in networks with lots of jitter. We will also set <code>tcp_tw_reuse</code> to tell the kernel it can reuse sockets in the <code>TIME_WAIT</code> state.</li>
 	<li>Increase the port range for ephemeral (outgoing) ports, by lowering the minimum port to 10000 (normally 32768), and raising the maximum port to 65000 (normally 61000). <em>Important:</em> This means you can’t have server software that attempts to bind to a port above 9999! If you need to bind to a higher port, say 10075, just modify this port range appropriately.</li>
 	<li>Increase the read/write TCP buffers (<code>tcp_rmem</code> and <code>tcp_wmem</code>) to allow for larger window sizes. This enables more data to be transferred without ACKs, increasing throughput. We won’t tune the total TCP memory (<code>tcp_mem</code>), since this is automatically tuned based on available memory by Linux.</li>
 	<li>Decrease the VM <code>swappiness</code> parameter, which discourages the kernel from swapping memory to disk. By default, Linux attempts to swap out idle processes fairly aggressively, which is counterproductive for long-running server processes that desire low latency.</li>
 	<li>Increase the TCP congestion window, and disable reverting to TCP slow start after the connection is idle. By default, TCP starts with a single small segment, gradually increasing it by one each time. This results in unnecessary slowness that impacts the start of every request – which is especially bad for HTTP.</li>
</ul>
<h2 id="kernel_parameters">Kernel Parameters</h2>
To start, edit <code>/etc/sysctl.conf</code> and add these lines:
<pre><code># /etc/sysctl.conf
# Increase system file descriptor limit
fs.file-max = 100000

# Discourage Linux from swapping idle processes to disk (default = 60)
vm.swappiness = 10

# Increase ephermeral IP ports
net.ipv4.ip_local_port_range = 10000 65000

# Increase Linux autotuning TCP buffer limits
# Set max to 16MB for 1GE and 32M (33554432) or 54M (56623104) for 10GE
# Don't set tcp_mem itself! Let the kernel scale it based on RAM.
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.core.rmem_default = 16777216
net.core.wmem_default = 16777216
net.core.optmem_max = 40960
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216

# Make room for more TIME_WAIT sockets due to more clients,
# and allow them to be reused if we run out of sockets
# Also increase the max packet backlog
net.core.netdev_max_backlog = 50000
net.ipv4.tcp_max_syn_backlog = 30000
net.ipv4.tcp_max_tw_buckets = 2000000
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 10

# Disable TCP slow start on idle connections
net.ipv4.tcp_slow_start_after_idle = 0

# If your servers talk UDP, also up these limits
net.ipv4.udp_rmem_min = 8192
net.ipv4.udp_wmem_min = 8192

# Disable source routing and redirects
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.accept_source_route = 0

# Log packets with impossible addresses for security
net.ipv4.conf.all.log_martians = 1</code></pre>
Since some of these settings can be cached by networking services, it’s best to reboot to apply them properly (<code>sysctl -p</code>does not work reliably).
<h2 id="open_file_descriptors">Open File Descriptors</h2>
In addition to the Linux <code>fs.file-max</code> kernel setting above, we need to edit a few more files to increase the file descriptor limits. The reason is the above just sets an absolute max, but we still need to tell the shell what our per-user session limits are.

So, first edit <code>/etc/security/limits.conf</code> to increase our session limits:
<pre><code># /etc/security/limits.conf
# allow all users to open 100000 files
# alternatively, replace * with an explicit username
* soft nofile 100000
* hard nofile 100000</code></pre>
Next, <code>/etc/ssh/sshd_config</code> needs to make sure to use PAM:
<pre><code># /etc/ssh/sshd_config
# ensure we consult pam
UsePAM yes</code></pre>
And finally, <code>/etc/pam.d/sshd</code> needs to load the modified <code>limits.conf</code>:
<pre><code># /etc/pam.d/sshd
# ensure pam includes our limits
session required pam_limits.so</code></pre>
You can confirm these settings have taken effect by opening a new ssh connection to the box and checking <code>ulimit</code>:
<pre><code>$ ulimit -n
100000</code></pre>
Why Linux has evolved to require 4 different settings in 4 different files is beyond me, but that’s a topic for a different post. <img class="emoji" draggable="false" src="https://s.w.org/images/core/emoji/11/svg/1f642.svg" alt="?" />
<h2 id="tcp_congestion_window">TCP Congestion Window</h2>
Finally, let’s increase the TCP congestion window from 1 to 10 segments. This is done on the interface, which makes it a more manual process that our <code>sysctl</code> settings. First, use <code>ip route</code> to find the default route, shown in bold below:
<pre><code>$ ip route
default via 10.248.77.193 dev eth0 proto kernel
10.248.77.192/26 dev eth0  proto kernel  scope link  src 10.248.77.212</code></pre>
Copy that line, and paste it back to the <code>ip route change</code> command, adding <code>initcwnd 10</code> to the end to increase the congestion window:
<pre><code>$ sudo ip route change default via 10.248.77.193 dev eth0 proto kernel initcwnd 10</code></pre>
To make this persistent across reboots, you’ll need to add a few lines of bash like the following to a startup script somewhere. Often the easiest candidate is just pasting these lines into <code>/etc/rc.local</code>:
<pre><code>defrt=`ip route | grep "^default" | head -1`
ip route change $defrt initcwnd 10</code></pre>
Once you’re done with all these changes, you’ll need to either bundle a new machine image or integrate these changes into a system configuration management tools.

</div>