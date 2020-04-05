---
id: 699
title: Sample Tcpdump Examples
date: 2017-11-08T15:45:15+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=699
permalink: /sample-tcpdump-examples/
categories:
  - DevOps
---
<h4>Breaking down the Tcpdump Command Line</h4>
The following command uses common parameters often seen when wielding the <code>tcpdump</code> scalpel.
<pre>:~$ sudo tcpdump -i eth0 -nn -s0 -v <span class="pre-blue">port 80</span></pre>
<strong>-</strong>i : Select interface that the capture is to take place on, this will often be an ethernet card or wireless adapter but could also be a <code>vlan</code> or something more unusual. Not always required if there is only one network adapter.
<strong>-</strong>nn : A single (<strong>n</strong>) will not resolve hostnames. A double (<strong>nn</strong>) will not resolve hostnames or ports. This is handy for not only viewing the IP / port numbers but also when capturing a large amount of data, as the <strong>name resolution will slow down the capture</strong>.
<strong>-</strong>s0 : Snap length, is the size of the packet to capture. <code>-s0</code> will set the size to unlimited - use this if you want to capture all the traffic. Needed if you want to pull binaries / files from network traffic.
<strong>-</strong>v : Verbose, using (<strong>-v</strong>) or (<strong>-vv</strong>) increases the amount of detail shown in the output, often showing more protocol specific information.
<span class="pre-blue">port 80</span> : this is a common port filter to capture only traffic on <strong>port 80</strong>, that is of course usually HTTP.
<h4>Display ASCII text</h4>
Adding <code>-A</code> to the command line will have the output include the <code>ascii</code> strings from the capture. This allows easy reading and the ability to parse the output using <code>grep</code> or other commands. Another option that shows both hexadecimal output and ASCII is the <code>-X</code> option.
<pre>:~$ sudo tcpdump <span class="pre-blue">-A</span> -s0 port 80</pre>
<h4>Capture on Protocol</h4>
Filter on UDP traffic. Another way to specify this is to use <strong>protocol 17</strong> that is <code>udp</code>. These two commands will produce the same result. The equivalent of the <code>tcp</code> filter is <strong>protocol 6</strong>.
<pre>:~$ sudo tcpdump -i eth0 <span class="pre-blue">udp</span>
:~$ sudo tcpdump -i eth0 <span class="pre-blue">proto 17</span></pre>
<h4>Capture Hosts based on IP address</h4>
Using the <code>host</code> filter will capture traffic going to (destination) and from (source) the IP address.
<pre>:~$ sudo tcpdump -i eth0 <span class="pre-blue">host 10.10.1.1</span></pre>
Alternatively capture only packets going one way using <code>src</code> or <code>dst</code>.
<pre>:~$ sudo tcpdump -i eth0 <span class="pre-blue">dst 10.10.1.20</span></pre>
<h4>Write a capture file</h4>
Writing a standard <code>pcap</code> file is a common command option. Writing a capture file to disk allows the file to be opened in Wireshark or other packet analysis tools.
<pre>:~$ sudo tcpdump -i eth0 -s0 <span class="pre-blue">-w test.pcap</span></pre>
<h4>Line Buffered Mode</h4>
Without the option to force line (<code>-l</code>) buffered (or packet buffered <code>-C</code>) mode you will not always get the <strong>expected response when piping</strong> the <code>tcpdump</code> output to another command such as <code>grep</code>. By using this option the output is sent immediately to the piped command giving an immediate response when troubleshooting.
<pre>:~$ sudo tcpdump -i eth0 -s0 <span class="pre-blue">-l</span> port 80 | grep 'Server:'</pre>
<h4>Combine Filters</h4>
Throughout these examples you can use standard logic to combine different filters.
<pre><strong>and</strong> or <strong>&amp;&amp;</strong>
<strong>or</strong> or <strong>||</strong>
<strong>not</strong> or <strong>!</strong></pre>
<h2>Practical Examples</h2>
In many of these examples there are a number of ways that the result could be achieved. As seen in some of the examples it is possible to focus the capture right down to individual bits in the packet.

The method you will use will depend on your desired output and how much traffic is on the wire. Capturing on a busy gigabit link may force you to use specific low level packet filters.

When troubleshooting you often simply want to get a result. Filtering on the port and selecting <strong>ascii</strong> output in combination with <code>grep</code>, <code>cut</code> or <code>awk</code> will often get that result. You can always go deeper into the packet if required.

For example when capturing HTTP requests and responses you could filter out all packets except the data by removing <strong>SYN /ACK / FIN</strong> however if you are using <code>grep</code> the noise will be filtered anyway. Keep it simple.

This can be seen in the following examples, where the aim is to get a result in the simplest (and therefore fastest) manner.
<h3>1. Extract HTTP User Agents</h3>
Extract HTTP User Agent from HTTP request header.
<pre>:~$ sudo tcpdump -nn -A -s1500 -l | <span class="pre-blue">grep "User-Agent:"</span></pre>
By using <code>egrep</code> and multiple matches we can get the User Agent and the Host (or any other header) from the request.
<pre>:~$ sudo tcpdump -nn -A -s1500 -l | <span class="pre-blue">egrep -i 'User-Agent:|Host:'</span></pre>
<h3>2. Capture only HTTP GET and POST packets</h3>
Going deep on the filter we can specify only packets that match GET.
<pre>:~$ sudo tcpdump -s 0 -A -vv <span class="pre-blue">'tcp[((tcp[12:1] &amp; 0xf0) &gt;&gt; 2):4] = 0x47455420'</span></pre>
Alternatively we can select only on POST requests. Note that the POST data may not be included in the packet captured with this filter. It is likely that a POST request will be split across multiple TCP data packets.
<pre>:~$ sudo tcpdump -s 0 -A -vv <span class="pre-blue">'tcp[((tcp[12:1] &amp; 0xf0) &gt;&gt; 2):4] = 0x504f5354'</span></pre>
The hexadecimal being matched in these expressions matches the ascii for GET and POST.

As an explanation <strong>tcp[((tcp[12:1] &amp; 0xf0) &gt;&gt; 2):4]</strong> first <a href="https://security.stackexchange.com/questions/121011/wireshark-tcp-filter-tcptcp121-0xf0-24">determines the location of the bytes</a> we are interested in (after the TCP header) and then selects the 4 bytes we wish to match against.
<h3>3. Extract HTTP Request URL's</h3>
Simply parse <strong>Host</strong> and <strong>HTTP Request location</strong> from traffic. By not targeting <strong>port 80</strong> we may find these requests on any port such as HTTP services running on high ports.
<pre>:~$ sudo tcpdump -s 0 -v -n -l | <span class="pre-blue">egrep -i "POST /|GET /|Host:"</span>

tcpdump: listening on enp7s0, link-type EN10MB (Ethernet), capture size 262144 bytes
	<span class="pre-blue">POST /</span>wp-login.php HTTP/1.1
	<span class="pre-blue">Host:</span> dev.example.com
	<span class="pre-blue">GET /</span>wp-login.php HTTP/1.1
	<span class="pre-blue">Host:</span> dev.example.com
	<span class="pre-blue">GET /</span>favicon.ico HTTP/1.1
	<span class="pre-blue">Host:</span> dev.example.com
	<span class="pre-blue">GET /</span> HTTP/1.1
	<span class="pre-blue">Host:</span> dev.example.com
</pre>
<h3>4. Extract HTTP Passwords in POST Requests</h3>
Lets get some passwords from the POST data. Will include Host: and request location so we know what the password is used for.
<pre>:~$ sudo tcpdump -s 0 -A -n -l | <span class="pre-blue">egrep -i "POST /|pwd=|passwd=|password=|Host:"</span>

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp7s0, link-type EN10MB (Ethernet), capture size 262144 bytes
11:25:54.799014 IP 10.10.1.30.39224 &gt; 10.10.1.125.80: Flags [P.], seq 1458768667:1458770008, ack 2440130792, win 704, options [nop,nop,TS val 461552632 ecr 208900561], length 1341: HTTP: POST /wp-login.php HTTP/1.1
.....s..<span class="pre-blue">POST /</span>wp-login.php HTTP/1.1
Host: dev.example.com
.....s..log=admin&amp;<span class="pre-blue">pwd=notmypassword</span>&amp;wp-submit=Log+In&amp;redirect_to=http%3A%2F%2Fdev.example.com%2Fwp-admin%2F&amp;testcookie=1</pre>
<h3>5. Capture Cookies from Server and from Client</h3>
MMMmmm Cookies! Capture cookies from the server by searching on Set-Cookie: (from Server) and Cookie: (from Client).
<pre>:~$ sudo tcpdump -nn -A -s0 -l | <span class="pre-blue">egrep -i 'Set-Cookie|Host:|Cookie:'</span>

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlp58s0, link-type EN10MB (Ethernet), capture size 262144 bytes
Host: dev.example.com
<span class="pre-blue">Cookie:</span> wordpress_86be02xxxxxxxxxxxxxxxxxxxc43=admin%7C152xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxfb3e15c744fdd6; _ga=GA1.2.21343434343421934; _gid=GA1.2.927343434349426; wordpress_test_cookie=WP+Cookie+check; wordpress_logged_in_86be654654645645645654645653fc43=admin%7C15275102testtesttesttestab7a61e; wp-settings-time-1=1527337439</pre>
<h3>6. Capture all ICMP packets</h3>
See all <code>ICMP</code> packets on the wire.
<pre>:~$ sudo tcpdump -n <span class="pre-blue">icmp</span>

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp7s0, link-type EN10MB (Ethernet), capture size 262144 bytes
11:34:21.590380 IP 10.10.1.217 &gt; 10.10.1.30: ICMP echo request, id 27948, seq 1, length 64
11:34:21.590434 IP 10.10.1.30 &gt; 10.10.1.217: ICMP echo reply, id 27948, seq 1, length 64
11:34:27.680307 IP 10.10.1.159 &gt; 10.10.1.1: ICMP 10.10.1.189 udp port 59619 unreachable, length 115
</pre>
<h3>7. Show ICMP Packets that are not ECHO/REPLY (standard ping)</h3>
Filter on the <code>icmp</code> type to select on <code>icmp</code> packets that are not standard <code>ping</code> packets.
<pre>:~$ sudo tcpdump <span class="pre-blue">'icmp[icmptype] != icmp-echo and icmp[icmptype] != icmp-echoreply'</span>

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp7s0, link-type EN10MB (Ethernet), capture size 262144 bytes
11:37:04.041037 IP 10.10.1.189 &gt; 10.10.1.20: ICMP 10.10.1.189 udp port 36078 unreachable, length 156
</pre>
<h3>8. Capture SMTP / POP3 Email</h3>
It is possible to extract email body and other data, in this example we are only parsing the email recipients.
<pre>:~$ sudo tcpdump -nn -l port 25 | <span class="pre-blue">grep -i 'MAIL FROM\|RCPT TO'</span></pre>
<h3>9. Troubleshooting NTP Query and Response</h3>
In this example we see the NTP query and response.
<pre>:~$ sudo tcpdump <span class="pre-blue">dst port 123</span>

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
21:02:19.112502 IP test33.ntp &gt; 199.30.140.74.<span class="pre-blue">ntp</span>: NTPv4, Client, length 48
21:02:19.113888 IP 216.239.35.0.ntp &gt; test33.<span class="pre-blue">ntp</span>: NTPv4, Server, length 48
21:02:20.150347 IP test33.ntp &gt; 216.239.35.0.<span class="pre-blue">ntp</span>: NTPv4, Client, length 48
21:02:20.150991 IP 216.239.35.0.ntp &gt; test33.<span class="pre-blue">ntp</span>: NTPv4, Server, length 48</pre>
<h3>10. Capture SNMP Query and Response</h3>
Using <code>onesixtyone</code> the fast SNMP protocol scanner we test an SNMP service on our local network and capture the <code>GetRequest</code> and <code>GetResponse</code>. For anyone who has had the (dis)pleasure of troubleshooting SNMP, this is a great way to see exactly what is happening on the wire. You can see the <code>OID</code> clearly in the traffic, very helpful when wrestling with <code>MIBS</code>.
<pre>:~$ onesixtyone 10.10.1.10 public

Scanning 1 hosts, 1 communities
10.10.1.10 [public] Linux test33 4.15.0-20-generic #21-Ubuntu SMP Tue Apr 24 06:16:15 UTC 2018 x86_64
</pre>
<pre>:~$ sudo tcpdump -n -s0  <span class="pre-blue">port 161 and udp</span>
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlp58s0, link-type EN10MB (Ethernet), capture size 262144 bytes
23:39:13.725522 IP 10.10.1.159.36826 &gt; 10.10.1.20.<span class="pre-blue">161</span>:  GetRequest(28)  .1.3.6.1.2.1.1.1.0
23:39:13.728789 IP 10.10.1.20.<span class="pre-blue">161</span> &gt; 10.10.1.159.36826:  GetResponse(109)  .1.3.6.1.2.1.1.1.0="Linux testmachine 4.15.0-20-generic #21-Ubuntu SMP Tue Apr 24 06:16:15 UTC 2018 x86_64"
</pre>
<h3>11. Capture FTP Credentials and Commands</h3>
Capturing FTP commands and login details is straight forward. After the authentication is established an FTP session can be <strong>active</strong> or <strong>passive</strong> this will determine whether the data part of the session is conducted over TCP port 20 or another ephemeral port. With the following command you will USER and PASS in the output (which could be fed to grep) as well as the FTP commands such as LIST, CWD and PASSIVE.
<pre>:~$ sudo tcpdump -nn -v <span class="pre-blue">port ftp or ftp-data</span></pre>
<h3>12. Rotate Capture Files</h3>
When capturing large amounts of traffic or over a long period of time it can be helpful to automatically create new files of a fixed size. This is done using the parameters <code>-W</code>, <code>-G</code> and <code>-C</code>.

In this command the file <strong>capture-(hour).pcap</strong> will be created every (-G) <strong>3600 seconds</strong> (1 hour). The files will be overwritten the following day. So you should end up with <strong>capture-{1-24}.pcap</strong>, if the hour was <strong>15</strong> the new file is (<strong>/tmp/capture-15.pcap</strong>).
<pre>:~$ tcpdump  <span class="pre-blue">-w /tmp/capture-%H.pcap -G 3600 -C 200</span></pre>
<h3>13. Capture IPv6 Traffic</h3>
Capture IPv6 traffic using the <code>ip6</code> filter. In these examples we have specified the TCP and UDP protocols using <code>proto 6</code> and <code>proto 17</code>.
<pre>tcpdump -nn <span class="pre-blue">ip6</span> proto 6</pre>
IPv6 with UDP and reading from a previously saved capture file.
<pre>tcpdump -nr ipv6-test.pcap <span class="pre-blue">ip6</span> proto 17</pre>
<h3>14. Detect Port Scan in Network Traffic</h3>
In the following example you can see the traffic coming from a single source to a single destination. The Flags [S] and [R] can be seen and matched against a seemingly random series of destination ports. These ports are seen in the RESET that is sent when the SYN finds a closed port on the destination system. This is standard behaviour for a port scan by a tool such as <a href="https://hackertarget.com/nmap-online-port-scanner/">Nmap</a>.

We have another <a href="https://hackertarget.com/nmap-tutorial/">tutorial on Nmap</a> that details captured port scans (<strong>open / closed / filtered</strong>) in a number of Wireshark captures.
<pre>:~$ tcpdump -nn

21:46:19.693601 IP 10.10.1.10.60460 &gt; 10.10.1.199.<span class="pre-blue">5432</span>: Flags [S], seq 116466344, win 29200, options [mss 1460,sackOK,TS val 3547090332 ecr 0,nop,wscale 7], length 0
21:46:19.693626 IP 10.10.1.10.35470 &gt; 10.10.1.199.<span class="pre-blue">513</span>: Flags [S], seq 3400074709, win 29200, options [mss 1460,sackOK,TS val 3547090332 ecr 0,nop,wscale 7], length 0
21:46:19.693762 IP 10.10.1.10.44244 &gt; 10.10.1.199.<span class="pre-blue">389</span>: Flags [S], seq 2214070267, win 29200, options [mss 1460,sackOK,TS val 3547090333 ecr 0,nop,wscale 7], length 0
21:46:19.693772 IP 10.10.1.199.389 &gt; 10.10.1.10.44244: Flags [R.], seq 0, ack 2214070268, win 0, length 0
21:46:19.693783 IP 10.10.1.10.35172 &gt; 10.10.1.199.<span class="pre-blue">1433</span>: Flags [S], seq 2358257571, win 29200, options [mss 1460,sackOK,TS val 3547090333 ecr 0,nop,wscale 7], length 0
21:46:19.693826 IP 10.10.1.10.33022 &gt; 10.10.1.199.<span class="pre-blue">49153</span>: Flags [S], seq 2406028551, win 29200, options [mss 1460,sackOK,TS val 3547090333 ecr 0,nop,wscale 7], length 0
21:46:19.695567 IP 10.10.1.10.55130 &gt; 10.10.1.199.<span class="pre-blue">49154</span>: Flags [S], seq 3230403372, win 29200, options [mss 1460,sackOK,TS val 3547090334 ecr 0,nop,wscale 7], length 0
21:46:19.695590 IP 10.10.1.199.49154 &gt; 10.10.1.10.55130: Flags [R.], seq 0, ack 3230403373, win 0, length 0
21:46:19.695608 IP 10.10.1.10.33460 &gt; 10.10.1.199.<span class="pre-blue">4915</span>2: Flags [S], seq 3289070068, win 29200, options [mss 1460,sackOK,TS val 3547090335 ecr 0,nop,wscale 7], length 0
21:46:19.695622 IP 10.10.1.199.49152 &gt; 10.10.1.10.33460: Flags [R.], seq 0, ack 3289070069, win 0, length 0
21:46:19.695637 IP 10.10.1.10.34940 &gt; 10.10.1.199.<span class="pre-blue">1029</span>: Flags [S], seq 140319147, win 29200, options [mss 1460,sackOK,TS val 3547090335 ecr 0,nop,wscale 7], length 0
21:46:19.695650 IP 10.10.1.199.1029 &gt; 10.10.1.10.34940: Flags [R.], seq 0, ack 140319148, win 0, length 0
21:46:19.695664 IP 10.10.1.10.45648 &gt; 10.10.1.199.<span class="pre-blue">5060</span>: Flags [S], seq 2203629201, win 29200, options [mss 1460,sackOK,TS val 3547090335 ecr 0,nop,wscale 7], length 0
21:46:19.695775 IP 10.10.1.10.49028 &gt; 10.10.1.199.<span class="pre-blue">2000</span>: Flags [S], seq 635990431, win 29200, options [mss 1460,sackOK,TS val 3547090335 ecr 0,nop,wscale 7], length 0
21:46:19.695790 IP 10.10.1.199.2000 &gt; 10.10.1.10.49028: Flags [R.], seq 0, ack 635990432, win 0, length 0
</pre>
<h3>15. Example Filter Showing Nmap NSE Script Testing</h3>
In this example the Nmap NSE script <code>http-enum.nse</code> is shown testing for valid urls against an open HTTP service.

On the Nmap machine:
<pre>:~$ nmap -p 80 --script=http-enum.nse targetip</pre>
On the target machine:
<pre>:~$ tcpdump -nn port 80 | <span class="pre-blue">grep "GET /"</span>

<span class="pre-blue">GET /</span>w3perl/ HTTP/1.1
<span class="pre-blue">GET /</span>w-agora/ HTTP/1.1
<span class="pre-blue">GET /</span>way-board/ HTTP/1.1
<span class="pre-blue">GET /</span>web800fo/ HTTP/1.1
<span class="pre-blue">GET /</span>webaccess/ HTTP/1.1
<span class="pre-blue">GET /</span>webadmin/ HTTP/1.1
<span class="pre-blue">GET /</span>webAdmin/ HTTP/1.1
</pre>
<h3>16. Capture Start and End Packets of every non-local host</h3>
This example is straight out of the <code>tcpdump</code> man page. By selecting on the <code>tcp-syn</code> and <code>tcp-fin</code> packets we can show each established TCP conversation with timestamps but without the data. As with many filters this allows the amount of noise to be reduced in order to focus in on the information that you care about.
<pre>:~$ tcpdump 'tcp[tcpflags] &amp; (tcp-syn|tcp-fin) != 0 and not src and dst net localnet'</pre>
<h3>17. Capture DNS Request and Response</h3>
Outbound DNS request to Google public DNS and the A record (ip address) response can be seen in this capture.
<pre>:~$ sudo tcpdump -i wlp58s0 -s0 <span class="pre-blue">port 53</span>

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlp58s0, link-type EN10MB (Ethernet), capture size 262144 bytes
14:19:06.879799 IP test.53852 &gt; google-public-dns-a.google.com.<span class="pre-blue">domain</span>: 26977+ [1au] A? play.google.com. (44)
14:19:07.022618 IP google-public-dns-a.google.com.<span class="pre-blue">domain</span> &gt; test.53852: 26977 1/0/1 A 216.58.203.110 (60)</pre>
<h3>18. Capture HTTP data packets</h3>
Only capture on HTTP data packets on port 80. Avoid capturing the TCP session setup (SYN / FIN / ACK).
<pre>tcpdump 'tcp port 80 and <span class="pre-blue">(((ip[2:2] - ((ip[0]&amp;0xf)&lt;&lt;2)) - ((tcp[12]&amp;0xf0)&gt;&gt;2)) != 0)'</span></pre>
<h3>19. Capture with tcpdump and view in Wireshark</h3>
Parsing and analysis of full application streams such as HTTP are much easier to perform with <a title="Wireshark Tutorial" href="https://hackertarget.com/wireshark-tutorial-and-cheat-sheet/">Wireshark</a> (or <a href="https://hackertarget.com/tshark-tutorial-and-filter-examples/">tshark</a>) rather than<code>tcpdump</code> .It is often more practical to capture traffic on a remote system using with<code>tcpdump</code> the write file option. Then copy them <code>pcap</code> to the local workstation for analysis with Wireshark.

Other than manually moving the file from the remote system to the local workstation it is possible to feed the capture to Wireshark over the SSH connection in real time. This <strong>tip is a favorite</strong>, pipe the raw output<code>tcpdump</code> right into on<code>wireshark</code> your local machine. <strong>Don't forget</strong> the so<code>not port 22</code> you are not capturing your SSH traffic.
<pre>:~$ ssh root@remotesystem 'tcpdump -s0 -c 1000 -nn -w - not port 22' | <span class="pre-blue">wireshark -k -i -</span></pre>
Another tip is to use count <code>-c</code> on the remote <code>tcpdump</code> to allow the capture to finish otherwise hitting <code>ctrl-c</code> will not only kill <code>tcpdump</code> but also Wireshark and your capture.
<h3>20. Top Hosts by Packets</h3>
List the top talkers for a period of time or number of packets. Using simple command line field extraction to get the IP address, sort and count the occurrences. Capture is limited by the count option.<code>-c</code>
<pre>sudo tcpdump -nnn -t <span class="pre-blue">-c 200</span> | cut -f 1,2,3,4 -d '.' | sort | uniq -c | sort -nr | head -n 20

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp7s0, link-type EN10MB (Ethernet), capture size 262144 bytes
<span class="pre-blue">200</span> packets captured
261 packets received by filter
0 packets dropped by kernel
    108 IP 10.10.211.181
     91 IP 10.10.1.30
      1 IP 10.10.1.50
</pre>
<h3>21. Capture all the plaintext passwords</h3>
In this command, we are focusing on standard plain text protocols and choosing to grep on anything user or password related. By selecting the <code>-B5</code> option on <code>grep</code> the aim is to get the preceding 5 lines that may provide context around the captured password (hostname, IP address, system).
<pre>:~$ sudo tcpdump port http or port ftp or port smtp or port imap or port pop3 or port telnet -l -A | egrep -i -B5 'pass=|pwd=|log=|login=|user=|username=|pw=|passw=|passwd=|password=|pass:|user:|username:|password:|login:|pass |user '</pre>
<h3>22. DHCP Example</h3>
And our final <code>tcpdump</code> example is for monitoring DHCP request and reply. DHCP requests are seen on <strong>port 67</strong> and the reply is on <strong>68</strong>. Using the verbose parameter <code>-v</code> we get to see the protocol options and other details.
<pre>:~$ sudo tcpdump -v -n <span class="pre-blue">port 67 or 68</span>

tcpdump: listening on enp7s0, link-type EN10MB (Ethernet), capture size 262144 bytes
14:37:50.059662 IP (tos 0x10, ttl 128, id 0, offset 0, flags [none], proto UDP (17), length 328)
    0.0.0.0.68 &gt; 255.255.255.255.<span class="pre-blue">67</span>: BOOTP/DHCP, Request from 00:0c:xx:xx:xx:d5, length 300, xid 0xc9779c2a, Flags [none]
	  Client-Ethernet-Address 00:0c:xx:xx:xx:d5
	  Vendor-rfc1048 Extensions
	    Magic Cookie 0x63825363
	    DHCP-Message Option 53, length 1: Request
	    Requested-IP Option 50, length 4: 10.10.1.163
	    Hostname Option 12, length 14: "test-ubuntu"
	    Parameter-Request Option 55, length 16: 
	      Subnet-Mask, BR, Time-Zone, Default-Gateway
	      Domain-Name, Domain-Name-Server, Option 119, Hostname
	      Netbios-Name-Server, Netbios-Scope, MTU, Classless-Static-Route
	      NTP, Classless-Static-Route-Microsoft, Static-Route, Option 252
14:37:50.059667 IP (tos 0x10, ttl 128, id 0, offset 0, flags [none], proto UDP (17), length 328)
    0.0.0.0.68 &gt; 255.255.255.255.<span class="pre-blue">67</span>: BOOTP/DHCP, Request from 00:0c:xx:xx:xx:d5, length 300, xid 0xc9779c2a, Flags [none]
	  Client-Ethernet-Address 00:0c:xx:xx:xx:d5
	  Vendor-rfc1048 Extensions
	    Magic Cookie 0x63825363
	    DHCP-Message Option 53, length 1: Request
	    Requested-IP Option 50, length 4: 10.10.1.163
	    Hostname Option 12, length 14: "test-ubuntu"
	    Parameter-Request Option 55, length 16: 
	      Subnet-Mask, BR, Time-Zone, Default-Gateway
	      Domain-Name, Domain-Name-Server, Option 119, Hostname
	      Netbios-Name-Server, Netbios-Scope, MTU, Classless-Static-Route
	      NTP, Classless-Static-Route-Microsoft, Static-Route, Option 252
14:37:50.060780 IP (tos 0x0, ttl 64, id 53564, offset 0, flags [none], proto UDP (17), length 339)
    10.10.1.1.67 &gt; 10.10.1.163.<span class="pre-blue">68</span>: BOOTP/DHCP, Reply, length 311, xid 0xc9779c2a, Flags [none]
	  Your-IP 10.10.1.163
	  Server-IP 10.10.1.1
	  Client-Ethernet-Address 00:0c:xx:xx:xx:d5
	  Vendor-rfc1048 Extensions
	    Magic Cookie 0x63825363
	    DHCP-Message Option 53, length 1: ACK
	    Server-ID Option 54, length 4: 10.10.1.1
	    Lease-Time Option 51, length 4: 86400
	    RN Option 58, length 4: 43200
	    RB Option 59, length 4: 75600
	    Subnet-Mask Option 1, length 4: 255.255.255.0
	    BR Option 28, length 4: 10.10.1.255
	    Domain-Name-Server Option 6, length 4: 10.10.1.1
	    Hostname Option 12, length 14: "test-ubuntu"
	    T252 Option 252, length 1: 10
	    Default-Gateway Option 3, length 4: 10.10.1.1
</pre>
&nbsp;