---
id: 774
title: Optimizing OpenVPN Throughput
date: 2018-10-01T03:09:17+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=774
permalink: /optimizing-openvpn-throughput/
published: false
categories:
  - DevOps
  - linux
  - security
  - unix
---

<h2 id="comp-lzo">comp-lzo</h2>
OpenVPN can use LZO to compress each packets individually. If we load compression module on one side, it must be loaded on the other side as well. Their <code>comp-lzo</code> mode however, do not need to be matched. It is generally best to set the <code>comp-lzo</code> mode to <code>adaptive</code> and let OpenVPN decides whether it should be used or not. There are circumstances however that using the module could hurt. In case of uncompressable data (e.g., transferring a zip file), <code>comp-lzo</code> could backfire by increasing the final packet size by 1. We can check the effectiveness of the LZO compression over time by sending <code>SIGUSR2</code> signal to the OpenVPN process (if its running as a daemon, the output would go to the syslog file). With such statistics you could decide whether using compression is useful for your link or not.
It is also worth noting that although LZO algorithm is fast, it still uses some resources. The algorithm is in such a way that compressing a block would require relatively more resource that decompressing it.
If we decide to not use compression, prevent loading the module completely by not including the <code>comp-lzo</code> line. Including it even when it’s set to <code>no</code>, would reduce your effective <code>tun-mtu</code> size by 1.
<h2 id="cipher-algorithm-and-size">Cipher algorithm and size</h2>
Different ciphers have different speeds in different hardwares(ie an <a href="https://en.wikipedia.org/wiki/AES_instruction_set" target="_blank" rel="noopener">AES-NI</a>capable CPU). This is a hard topic to cover as it is up to you to decide whether you’d want to sacrifice better encryption to a faster tunnel or using smaller keysize to reduce the CPU load. There are countless of articles about OpenSSL ciphers, their speed and their strength. Do a google search and get familiar with the subject. Just as a side note, to compare the ciphers speed in your platform take a look at <code>openssl speed -h</code> command.
<h2 id="sndbuf-and-rcvbuf">sndbuf and rcvbuf</h2>
There have been reports of speed improvement in some circumstances when these values are set to <code>0</code> <sup id="fnref-1" class="footnote-ref"><a href="https://hamy.io/post/0003/optimizing-openvpn-throughput/#fn:1">1</a></sup>. Discussion - https://www.lowendtalk.com/discussion/40099/why-openvpn-is-so-slow-cool-story
<h2 id="fast-io">fast-io</h2>
This little flag which is supported in non-windows systems improves CPU usage when dealing with UDP packets by using non-blocking write operations. It only applies to UDP tunnels.
<h2 id="mtu-adjustments">MTU adjustments</h2>
OpenVPN UDP packets should not be fragmented. So we need to ensure we are not sending a packet larger than what can travel to the other end of the tunnel. TCP tunnels usually don’t require such adjustments.
<h3 id="link-mtu">link-mtu</h3>
The maximum size of the final UDP packet after encapsulation minus the headers. So for example if your link MTU is <code>1500</code>, the correct value for <code>link-mtu</code>would be <code>1472</code> <em>(1500 - 20(IP header) - 8(UDP header))</em>. In the OpenVPN manual it is said that it’s best to not set this value directly. However in my experience, this is in fact the best way to adjust your tun/tap link MTU properly and the <code>tun-mtu</code>value (which we will discuss later) will be derived from that. The default value of <code>link-mtu</code> however is derived from <code>tun-mtu</code> and is bigger than 1500.
<h3 id="tun-mtu">tun-mtu</h3>
The actual MTU of OpenVPN tun/tap device. This defaults to <code>1500</code>.

We can only specify either <code>link-mtu</code> or <code>tun-mtu</code> and not both. The other one will be calculated internally by OpenVPN. One other thing to note is that <code>link-mtu</code>applies to final packets (after encryption and encapsulation) while <code>tun-mtu</code>applies to the unencrypted packets which are about to enter the tun/tap device.

The <code>tun-mtu</code> of <code>1500</code> is ideal if your physical link MTU could handle it. It would provide maximum compatibility between routers along the way. However this is not always the case. Although OpenVPN is supposed to be able to discover this and act accordingly but the whole thing would collapse if you have a broken <a href="https://en.wikipedia.org/wiki/Path_MTU_Discovery" target="_blank" rel="noopener">PMTUD</a>.
In such case, manual intervention for adjusting MTU is required. In another post I will talk about ways to find the correct MTU of a path but assuming you already know the correct value, you subtract <code>28 bytes</code> from it and set it as <code>link-mtu</code>value and let OpenVPN calculate the right <code>tun-mtu</code> for you. Again, remember, the calculated <code>tun-mtu</code> value applies to packets before compression/encapsulation and its size highly depends on other factors like cipher algorithm, keysize, compression module, etc.

In very fast links, setting <code>tun-mtu</code> to a high value could potentially help <sup id="fnref-2" class="footnote-ref"><a href="https://hamy.io/post/0003/optimizing-openvpn-throughput/#fn:2">2</a></sup>.
<h3 id="fragment">fragment</h3>
This option should generally be avoided when possible. It adds <code>4 bytes</code> of overhead to each packet. But it is there as the last resort when no other option works. With this option, OpenVPN internally fragments packets to chunks not bigger than the set value and send them over the link. The other end receives and reassembles them to create the original sent packet. This is the only instance I know that a single packet could result in more than one OpenVPN UDP packets being sent over the link.
<h3 id="mssfix">mssfix</h3>
This option only applies to TCP connections inside the tunnel. <em>Maximum Segment Size</em> is yet another feature of TCP. This option is negotiated between peers during TCP handshaking via SYN packets. It is the maximum size of the payload each TCP packet can carry. It does not take IP and TCP header sizes into account. This option can be used in a link with broken PMTUD to at least make TCP connections possible.
Even though MSS itself is a TCP feature, this OpenVPN option targets encapsulated UDP packets. Meaning it changes the MSS value of the TCP protocols inside the tunnel in a way that after UDP encryption/encapsulation the resulting UDP packet size (minues IP/UDP headers), would not exceed the <code>mssfix</code> value.
So in an optimized link, <code>mssfix</code> is either disabled (set to 0) or it’s value would be the same as <code>link-mtu</code>’s.
As a side note, <code>mssfix</code> applies to both sending AND receiving SYN packets so it is not an ideal solution for asymmetric links… but that’s for another post.

Ref: https://community.openvpn.net/openvpn/wiki/Gigabit_Networks_Linux