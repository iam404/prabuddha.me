---
id: 770
title: 'OpenVPN &#8211; TCP or UDP tunneling ?'
date: 2018-10-01T02:46:08+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=770
permalink: /openvpn-tcp-or-udp-tunneling/
categories:
  - linux
  - security
---
An OpenVPN tunnel runs over IP and can encapsulate VPN traffic into either a UDP or a TCP connection. To understand the pros and cons of each, we first need to have an understanding of them both.
<h3 id="tcp">TCP</h3>
<em>Transmission Control Protocol</em> is the dominant protocol there is for most daily stuff happening on a network. It has some very interesting features built-in which makes it very resistant to network packet loss, packet reordering, packet duplication, unintentional packet corruption, and even link congestion. Despite it being not perfect<sup id="fnref-1" class="footnote-ref"><a href="https://hamy.io/post/0002/openvpn---tcp-or-udp-tunneling/#fn:1">1</a></sup>, it’s survived the test of time and it’s not going anywhere in near future.

All those features, however, come at a price. A typical TCP packet has a header size of 20 bytes. Assuming you’re using IPv4, You also get a 20 bytes IP header added on top of it. So at least 40 bytes in each TCP packet is the header data that comes before the actual payload.
<h3 id="udp">UDP</h3>
Unlike TCP, <em>User Datagram Protocol</em> does not come with many features. It comes with a checksum header for packet integrity but connection reliably as a whole is not guaranteed. In fact, a UDP stream can hardly be called a connection at all. It’s just a stream of data going from one end to another. The other end of the stream might receive it, might not. Packets might be dropped, get out of order, get duplicated… . In other words, while TCP frees the applications from dealing with low-level protocol stack and complex underlying issues, UDP passes this response to the applications. If a program wants to use UDP for its communication, It also has to deal with all the potential network issues that might arise. As an example, it might not matter much if bunch of frames get dropped in a live camera feed (and certainly re-sending them is counter productive), hence using UDP to transfer a live stream could make sense.

Because of the limited feature set, UDP header is much smaller. It’s always a fixed 8 size in byte. Adding IPv4 header on top of it, we get total header size of 28 bytes.
<h2 id="why-you-d-want-to-use-udp-over-tcp-tunneling">Why you’d want to use UDP, over TCP tunneling</h2>
It might not be apparent at first but let’s go over some of the main reasons:
<ul>
 	<li><strong>Smaller packet size</strong>
We save an extra 12 bytes in each packet for the actual payload. Let’s not forget that OpenVPN encrypts the whole packet including its IP header and wraps it around another packet (which again also has its own headers) and on top of that, the encryption also adds an overhead. So the actual payload is going to be much smaller than your link MTU/MRU. Even increasing the packet size by a single byte could potentially reduce your pps(packets per second) rate in a fast enough link which would, in turn, make your link faster and more responsive.</li>
 	<li><strong>Overall connection’s reliability!</strong>
Connection’s reliability is already guaranteed by the upper layer protocol. Assume that you’re using your OpenVPN UDP tunnel to load a website. Your browser would use TCP to connect to the port 80 of the server hosting the website. The browser has no idea your connection is going through a UDP VPN tunnel and neither does the said TCP protocol. The TCP connection gets established and you get full advantage of all the features it has to offer. If the lower layer (The UDP stream) drops a packet, the TCP connection established inside it would notice and re-request it. There is absolutely no need for extra TCP connection on top of it to guarantee reliability and in fact it’s a plain bad idea (more on that later).</li>
 	<li><strong>Less resource usage</strong>
Because of its simplicity, processing a UDP packet requires less resource than a TCP one. Furthermore, unlike TCP, there is no such a thing as waiting for an ACK packet to arrive every <a href="https://en.wikipedia.org/wiki/TCP_tuning#Window_size" target="_blank" rel="noopener">RWIN</a> bytes. And on top of that, there is no handshake at all. One end just starts sending data to the other end. No formal introduction is required by the protocol<sup id="fnref-2" class="footnote-ref"><a href="https://hamy.io/post/0002/openvpn---tcp-or-udp-tunneling/#fn:2">2</a></sup>.</li>
 	<li><strong>Avoiding TCP meltdown effect</strong>
This one is a serious deal-breaker for TCP over TCP, be it used by OpenVPN or any other type of TCP tunneling like SSTP. TCP by itself is designed with congestion control and recovery in mind. It is adaptive to the condition of the link it’s running on and does a decent job in recovering from network mishaps. However, using TCP over TCP could easily cause a situation in which lower and upper layers (which both are running their own version of congestion control algorithm) start competing with each other and in fact worsening the situation at each try. This is especially true for slow links and could result in terribly slow connections and constant freezing<sup id="fnref-3" class="footnote-ref"><a href="https://hamy.io/post/0002/openvpn---tcp-or-udp-tunneling/#fn:3">3</a></sup>.</li>
</ul>
<h2 id="problems-with-using-udp">Problems with using UDP</h2>
<ul>
 	<li>Since UDP lacks any sort of formal handshake, the source IP address/port of the remote peer advertised in its header cannot be trusted. This doesn’t matter much however if OpenVPN encryption(be it SSL/TLS, or static key) is properly set up, as it completely makes up for that and more.</li>
 	<li>OpenVPN UDP packets have the DF(Don’t Fragment) flag set and should not be fragmented. A broken Path MTU as the result of a <a href="https://en.wikipedia.org/wiki/Black_hole_%28networking%29" target="_blank" rel="noopener">black hole</a> if not handled properly, could cripple an OpenVPN UDP tunnel.</li>
</ul>
<h2 id="when-to-use-tcp-tunneling">When to use TCP Tunneling</h2>
<ul>
 	<li><strong>When communication over UDP in your network is blocked</strong>
If you can’t access a UDP port, you can’t set up a UDP tunnel over it. As a side note, have you tried UDP port 53 or 123?</li>
 	<li><strong>When securing solely based on remote end IP address is needed</strong>
This should hardly ever be the case and only relying on source IP address validation for securing network access is not the wisest idea. But as I’ve mentioned earlier, in UDP tunneling, source IP address of the remote peer can be eaily spoofed. If for whatever reason you are not willing to use encryption for your OpenVPN instance, you probably want to at least limit it to trusted source IP addresses and use TCP.</li>
 	<li><strong>When the reliability of an unreliable stream is required</strong> to Assume that you come across a program that while it is sensitive to packet loss, it uses UDP for communication. Like an old Syslog server communicating over UDP port 514. You could, in theory, wrap an OpenVPN TCP tunnel around the whole thing and guarantee the UDP stream reliability to some good extent (Even if it becomes laggy). Or maybe you have a program that sends a single ping to the other end to make some decisions and you also do not have access to the source code to adjust the settings. An ICMP packet inside an active TCP tunnel would eventually get through even if it takes minutes!</li>
</ul>
And that’s it. Now you know the pros and cons of each protocol and are able to make an educated decision on the matter.

Source: http://sites.inka.de/bigred/devel/tcp-tcp.html