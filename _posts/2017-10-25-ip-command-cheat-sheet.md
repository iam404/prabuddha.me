---
id: 800
title: 'Linux &#8220;ip&#8221; command Cheat Sheet'
date: 2017-10-25T20:28:09+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=800
permalink: /ip-command-cheat-sheet/
categories:
  - linux
---
<pre><span class="pl-c"># add default gateway</span>
ip route add default via <span class="pl-smi">$GATEWAY_IP</span>

<span class="pl-c"># add static route</span>
ip route add <span class="pl-smi">$DST_NET</span> via <span class="pl-smi">$GATEWAY_IP</span> dev <span class="pl-smi">$DEV</span>

<span class="pl-c"># show routing table</span>
ip route show

<span class="pl-c"># add ip address to device</span>
ip addr add <span class="pl-smi">$IP</span>/<span class="pl-smi">$NETMASK</span> dev <span class="pl-smi">$DEV</span>

<span class="pl-c"># remove ip address from device</span>
ip addr del <span class="pl-smi">$IP</span>/<span class="pl-smi">$NETMASK</span> dev <span class="pl-smi">$DEV</span>

<span class="pl-c"># enable interface</span>
ip link <span class="pl-c1">set</span> <span class="pl-smi">$DEV</span> up

<span class="pl-c"># disable interface</span>
ip link <span class="pl-c1">set</span> <span class="pl-smi">$DEV</span> down</pre>
<h2>Configure Static IP Address</h2>
To configure static IP Address, you need to update or edit network configuration file to assign a Static IP Address to a system.

Assign Static IP Address to <strong>eth0</strong> interface editing configuration file <strong>/etc/network/interfaces</strong> to make permanent changes as shown below.
<pre>auto eth0
iface eth0 inet static
address 192.168.1.2
netmask 255.255.255.0
gateway 192.168.1.1</pre>
Next, restart network services.
<pre># /etc/init.d/networking restart</pre>
<pre>$ sudo /etc/init.d/networking restart</pre>
&nbsp;

*UPDATE*

[embeddoc url="https://prabuddha.me/wp-content/uploads/2018/10/rh_ip_command_cheatsheet_1214_jcs_print.pdf" download="all" viewer="google"]