---
id: 906
title: Diagnostic commands to identify Linux performance bottlenecks.
date: 2018-01-27T19:00:06+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=906
permalink: /diagnostic-commands-to-identify-linux-performance-bottlenecks/
categories:
  - DevOps
---
<blockquote>A troubleshooting performance related issue in IT world is always challenging, and if you were not aware of right tools, then it would be frustrating.</blockquote>
Tracking down Performance bottleneck in Linux can be done by monitoring the following hardware components:-
<ol>
 	<li>CPU speed</li>
 	<li>CPU usage</li>
 	<li>Disk bandwidth</li>
 	<li>Disk IOPS</li>
 	<li>Network bandwidth/throughput</li>
 	<li>Memory bandwidth</li>
 	<li>Memory Capacity</li>
</ol>
<a href="https://prabuddha.me/wp-content/uploads/2019/01/linux_observability_tools.png"><img class="aligncenter wp-image-907 size-full" src="https://prabuddha.me/wp-content/uploads/2019/01/linux_observability_tools.png" alt="" width="1500" height="1050" /></a>

<em>Below I’ve compiled performance monitoring and debugging tools that are helpful when we are working on Linux environment. This list is not comprehensive or authoritative by any means.</em>
<h3><a href="https://linux.die.net/man/1/top">TOP</a></h3>
<p class="line862">To run <tt class="backtick">top</tt>, just log into the server (using SSH) and run the command <tt class="backtick">top</tt> — it's that simple. It'll give you a whole screenful of information that you can interpret to send you on your way.<span id="line-44" class="anchor"></span><span id="line-45" class="anchor"></span></p>
<p class="line862">There are many parts to <tt class="backtick">top</tt>'s output; the following is a list of the parts of the output that I find useful, and what they mean.<span id="line-46" class="anchor"></span><span id="line-47" class="anchor"></span></p>
<p class="line867"><img class="attachment" title="top_example.png" src="http://web.archive.org/web/20101028025942im_/https://anchor.com.au/hosting/development/HuntingThePerformanceWumpus?action=AttachFile&amp;do=get&amp;target=top_example.png" alt="top_example.png" /><span id="line-48" class="anchor"></span><span id="line-49" class="anchor"></span></p>

<ul>
 	<li>
<p class="line891"><strong>Load Average:</strong> In the very top right hand corner is the label <tt class="backtick">load average</tt>, followed by three decimal numbers. The load average is simply the average number of processes that are waiting to use the CPU, averaged over the last minute, five minutes, and fifteen minutes respectively. When the numbers are all about the same (whether high or low), then the load on the system is consistent over a long(ish) period of time. When the first number is larger than the others, then the load is rising, and when the first number is smaller, the system load is falling.</p>
</li>
 	<li class="gap">
<p class="line891"><strong>CPU Usage:</strong> The third line indicate how your CPU(s) are being used. The summary version is a single line labelled "<tt class="backtick">Cpu(s)</tt>", while the detailed (per-CPU) information is given in multiple lines, labelled "<tt class="backtick">Cpu0</tt>", "<tt class="backtick">Cpu1</tt>", and so on. You can toggle between the two modes by pressing <tt class="backtick">1</tt>.<span id="line-54" class="anchor"></span><span id="line-55" class="anchor"></span></p>
<p class="line862">The CPU info line(s) have a bunch of different percentage numbers. Different versions of <tt class="backtick">top</tt> have different sets, but the common ones (and those which we're typically interested in) are:<span id="line-56" class="anchor"></span><span id="line-57" class="anchor"></span></p>

<ul>
 	<li>
<p class="line891"><strong>User time (us):</strong> This is how much of the CPU's time is spent processing userspace code (that is, your program, standard library calls, that sort of thing). If this is at or near 100%, then something on the system is burning a lot of pure CPU time — either your program, or some system management program, and it's doing things that are "pure computation" — not I/O, just calculations.<span id="line-58" class="anchor"></span></p>
</li>
 	<li>
<p class="line891"><strong>System time (sy):</strong> This is how much of the CPU's time is involved in doing things in the kernel. The kernel manages the disks, network devices, console (keyboard/monitor), memory management, and so on. If this is high then some program on the system is doing a lot of "kernel level" things, like I/O.<span id="line-59" class="anchor"></span></p>
</li>
 	<li>
<p class="line891"><strong>Waiting time (wa):</strong> As you probably know, disks aren't nearly as fast as the rest of your system, so when you need to get something off a disk or write something to one, the system has to wait a relative eternity for it to finish. The "waiting time" is just the percentage of time that the CPU is spending actively waiting for the disk to finish doing it's thing. If this is high, then something is working the disks hard — either an application, or the dreaded <strong>swap</strong> .<span id="line-60" class="anchor"></span></p>
</li>
 	<li>
<p class="line891"><strong>Idle time (id):</strong> How long the CPU spends just lounging around, not doing anything productive.<span id="line-61" class="anchor"></span><span id="line-62" class="anchor"></span></p>
</li>
</ul>
In summary: if the CPUs are working hard running code, the user and/or system time percentages will be high, while if the waiting time is high, then the system is waiting for disk activity a lot.<strong> If the CPUs are mostly idle, then the bottleneck is almost certainly not the hardware itself, but rather something in your program that is waiting on some other external resource.</strong><span id="line-64" class="anchor"></span></li>
 	<li class="gap">
<p class="line891"><strong>Memory Usage:</strong> Just below the CPU info is a couple of lines, labelled "<tt class="backtick">Mem</tt>" and "<tt class="backtick">Swap</tt>". These lines give you some (very) basic idea of where memory is being used on the system. By themselves, they don't tell you very much, but if your CPU percentages are skewed in certain ways, they can be of some use in narrowing down the root cause. <span id="line-65" class="anchor"></span><span id="line-66" class="anchor"></span>These tests are only really valid when there is very little free main memory. If you've got lots of free main memory, then something which was using a lot of memory probably just exited, and your analysis won't be accurate.<span id="line-67" class="anchor"></span><span id="line-68" class="anchor"></span></p>

<ul>
 	<li>
<p class="line891"><strong>High waiting time, very low buffers/cache:</strong> If the CPU is spending a lot of time waiting for the disk, and you have very low values for the <tt class="backtick">buffers</tt> and <tt class="backtick">cache</tt> (typically less than 20,000k each) then it's likely that part of the reason why your disks are slow is because there is very little memory available to cache disk data, and so the system is constantly going back to disk to re-read data it just read little while ago. Reduce your memory usage or install more RAM.<span id="line-69" class="anchor"></span><span id="line-70" class="anchor"></span></p>
</li>
 	<li class="gap">
<p class="line891"><strong>High waiting time, large amounts of swap used:</strong> The chances are that the system is swapping heavily (that is, writing a lot of pages of memory to disk so that other chunks of memory can be read from disk and worked on by a program). Again, reduce your memory usage or install more RAM, because your system is running out.<span id="line-71" class="anchor"></span><span id="line-72" class="anchor"></span></p>
</li>
</ul>
</li>
 	<li class="gap">
<p class="line891"><strong>Process list:</strong> The list of processes that are running on the system is what takes up most of the space on the screen, starting just below the "highlighted" line. The contents will change every couple of seconds, as <tt class="backtick">top</tt> collects a new set of data and displays it for you. By default, the list of processes is sorted by the amount of CPU time (the sum of the "user" and "system" CPU time) they're using, although you can change the sort order to pretty much anything you like. Things to look out for in the process list are:<span id="line-73" class="anchor"></span><span id="line-74" class="anchor"></span></p>

<ul>
 	<li>
<p class="line891"><strong>One process using 100% (or more) of CPU:</strong> If the CPU info showed that the CPUs were largely being consumed in user time, then there should be one (or more) processes in the list that are using all that CPU time, and they should be at the top of the list. If you're bottlenecked on CPU, knowing which process is using it all is obviously crucial.<span id="line-75" class="anchor"></span><span id="line-76" class="anchor"></span></p>
</li>
 	<li class="gap">
<p class="line891"><strong>One process using most of the memory:</strong> If the CPU and memory usage showed that there was memory pressure, then you should press <tt class="backtick">M</tt> (capital-m) to sort by memory usage. The big memory hogs should be clearly displayed at the top of the list. Don't worry so much about exactly how much memory they're using; it's more useful to identify who the hogs are, so they can be optimised. <span id="line-79" class="anchor"></span></p>
</li>
</ul>
</li>
</ul>
<h3 class="line862"><a href="https://linux.die.net/man/1/htop">HTOP</a></h3>
It is similar to top but allows you to scroll vertically and horizontally, so you can see all the processes running on the system, along with their full command lines.
<h3><a href="https://en.wikipedia.org/wiki/Vmstat">Vmstat -Virtual Memory Statistics</a></h3>
Linux <strong>VmStat</strong> command used to display statistics of <strong>virtual memory</strong>, <strong>kernel threads</strong>, <strong>disks</strong>, <strong>system processes</strong>, <strong>I/O blocks</strong>, <strong>interrupts</strong>, <strong>CPU activity</strong> and much more. By default, vmstat command is not available under Linux systems you need to install a package called <strong>sysstat</strong> that includes a vmstat program. The common usage of command format is.
<pre><strong># vmstat</strong>

procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
 r  b   swpd   free  inact active   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 810420  97380  70628    0    0   115     4   89   79  1  6 90  3  0</pre>
<h4><a href="https://linux.die.net/man/1/iotop">Iotop – Monitor Linux Disk I/O</a></h4>
<strong>Iotop</strong> is also much similar to <strong>top command</strong> and <strong>Htop program</strong>, but it has accounting function to monitor and display real-time <strong>Disk I/O</strong> and <strong>processes</strong>. This tool is much useful for finding the exact process and high used disk read/writes of the processes.
<pre><strong># iotop</strong></pre>
<div class="wp-caption aligncenter">

<img title="Iotop Command Example" src="https://www.tecmint.com/wp-content/uploads/2012/07/iotop-Screen.jpg" alt="Iotop Command Example" width="630" height="420" data-lazy-loaded="true" />
<p class="wp-caption-text">Iotop Command Example Screenshot</p>

</div>
<h4></h4>
<h4><a href="https://linux.die.net/man/1/iostat">Iostat – Input/Output Statistics</a></h4>
<strong>IoStat</strong> is simple tool that will collect and show system <strong>input</strong> and <strong>output</strong> storage device statistics. This tool is often used to trace storage device performance issues including <strong>devices</strong>, <strong>local disks</strong>, <strong>remote disks</strong> such as <strong>NFS</strong>.
<pre><strong># iostat</strong>

Linux 2.6.18-238.9.1.el5 (tecmint.com)         09/13/2012

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           2.60    3.65    1.04    4.29    0.00   88.42

Device:            tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
cciss/c0d0       17.79       545.80       256.52  855159769  401914750
cciss/c0d0p1      0.00         0.00         0.00       5459       3518
cciss/c0d0p2     16.45       533.97       245.18  836631746  384153384
cciss/c0d0p3      0.63         5.58         3.97    8737650    6215544
cciss/c0d0p4      0.00         0.00         0.00          8          0
cciss/c0d0p5      0.63         3.79         5.03    5936778    7882528
cciss/c0d0p6      0.08         2.46         2.34    3847771    3659776</pre>
&nbsp;
<h4><a href="https://linux.die.net/man/8/iftop">iftop – Network Bandwidth Monitoring</a></h4>
<strong>iftop</strong> is another terminal-based free open source system monitoring utility that displays a frequently updated list of network bandwidth utilization (source and destination hosts) that passing through the network interface on your system. iftop is considered for network usage, what ‘<strong>top</strong>‘ does for CPU usage. iftop is a ‘<strong>top</strong>‘ family tool that monitor a selected interface and displays a current bandwidth usage between two hosts.
<h4><a href="https://www.tcpdump.org/">Tcpdump – Network Packet Analyzer</a></h4>
<strong>Tcpdump</strong> one of the most widely used command-line <strong>network packet analyzer</strong> or <strong>packets sniffer</strong> program that is used capture or filter <strong>TCP/IP</strong> packets that received or transferred on a specific interface over a network. It also provides a option to save captured packages in a file for later analysis. tcpdump is almost available in all major Linux distributions.
<pre><strong># tcpdump -i eth0</strong>

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes
22:08:59.617628 IP tecmint.com.ssh &gt; 115.113.134.3.static-mumbai.vsnl.net.in.28472: P 2532133365:2532133481(116) ack 3561562349 win 9648
22:09:07.653466 IP tecmint.com.ssh &gt; 115.113.134.3.static-mumbai.vsnl.net.in.28472: P 116:232(116) ack 1 win 9648
22:08:59.617916 IP 115.113.134.3.static-mumbai.vsnl.net.in.28472 &gt; tecmint.com.ssh: . ack 116 win 64347</pre>
<h3><a href="https://linux.die.net/man/1/sar">Sar</a></h3>
System Activity Report is a Unix System V-derived system monitor command used to report on various system loads, including CPU activity, memory/paging, interrupts, device load, network, and swap space utilization. Sar uses /proc filesystem for gathering information.

Below command shows sar output for network debugging -
<pre>$ sar -n TCP,ETCP,DEV 1

Linux 4.4.0-121-generic (localhost)    01/27/2019     _x86_64_   (1 CPU)

07:42:50 PM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
07:42:51 PM      tun0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:42:51 PM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:42:51 PM veth8c009c0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:42:51 PM   docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:42:51 PM      eth0     10.10      0.00      0.65      0.00      0.00      0.00      0.00      0.00

07:42:50 PM  active/s passive/s    iseg/s    oseg/s
07:42:51 PM      0.00      0.00     10.10      0.00

07:42:50 PM  atmptf/s  estres/s retrans/s isegerr/s   orsts/s
07:42:51 PM      0.00      0.00      0.00      0.00      0.00</pre>
<h3><a href="https://linux.die.net/man/8/netstat">Netstat – Network Statistics</a></h3>
<strong>Netstat</strong> is a command line tool for monitoring <strong>incoming</strong> and <strong>outgoing network</strong> packets statistics as well as interface statistics. It is a very useful tool for every system administrator to monitor network performance and troubleshoot network related problems.
<pre><strong># netstat -a | more</strong>

Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address               Foreign Address             State
tcp        0      0 *:mysql                     *:*                         LISTEN
tcp        0      0 *:sunrpc                    *:*                         LISTEN
tcp        0      0 *:realm-rusd                *:*                         LISTEN
tcp        0      0 *:ftp                       *:*                         LISTEN
tcp        0      0 localhost.localdomain:ipp   *:*                         LISTEN
tcp        0      0 localhost.localdomain:smtp  *:*                         LISTEN
tcp        0      0 localhost.localdomain:smtp  localhost.localdomain:42709 TIME_WAIT
tcp        0      0 localhost.localdomain:smtp  localhost.localdomain:42710 TIME_WAIT
tcp        0      0 *:http                      *:*                         LISTEN
tcp        0      0 *:ssh                       *:*                         LISTEN
tcp        0      0 *:https                     *:*                         LISTEN</pre>
<h3><a href="https://linux.die.net/man/8/ss">SS</a></h3>
Socket Statistics. Shows information similar to <span class="code">netstat</span>, <span class="code">ss</span> can display more TCP and state information than other tools.
<pre>Syntax:

     ss [<i>options</i>] [<i>FILTER</i>]</pre>
<h3><a href="https://linux.die.net/man/1/free">free </a></h3>
The free command is the most simple and easy to use the command to check memory usage on Linux. Here is a quick example
<pre class="terminal">$ free -m
             total       used       free     shared    buffers     cached
Mem:          7976       6459       1517          0        865       2248
-/+ buffers/cache:       3344       4631
Swap:         1951          0       1951</pre>
The m option displays all data in MBs. The total os 7976 MB is the total amount of RAM installed on the system, that is 8GB. The used column shows the amount of RAM that has been used by linux, in this case around 6.4 GB. The output is pretty self explanatory. The catch over here is the cached and buffers column. The second line tells that 4.6 GB is free. This is the free memory in first line added with the buffers and cached amount of memory.

Linux has the habit of caching lots of things for faster performance, so that memory can be freed and used if needed.
The last line is the swap memory, which in this case is lying entirely free.
<h3><a href="https://pythonhosted.org/memtop">Memtop</a></h3>
Intended to help you to analyse and understand <strong>system memory utilization</strong> of your Linux box and check <strong>memory consumption of processes</strong> running there. It is intended to run in long-period iterations to let you see changes in overall memory utilization and consumption of individual processes.

Does not come out of box in linux. Can be installed following - https://pythonhosted.org/memtop/installation.html