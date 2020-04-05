---
id: 189
title: How to Locate a MAC Address in Mac OS X
date: 2016-05-14T14:28:18+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=189
permalink: /how-to-locate-a-mac-address-in-mac-os-x/
categories:
  - unix
---
In Mac OS X, your MAC (Media Access Control) address is distinct from the IP address assigned to your Mac, and is defined by the hardware of each Ethernet or AirPort interface.

To find your MAC address:
<ol>
 	<li>From the Apple menu, select <span class="menuitem"><strong>System Preferences</strong>...</span>.</li>
 	<li>In<strong> System Preferences</strong>, from the <span class="menuitem">View</span> menu, select <strong><span class="menuitem">Network</span>.</strong></li>
 	<li>In the left of the "<strong>Network</strong>" window that opens, click the name of your connection (e.g., <span class="menuitem">Wi-Fi</span>, <span class="menuitem">AirPort</span>, <span class="menuitem">Ethernet</span>, <span class="menuitem">Built-in Ethernet</span>).</li>
 	<li>Click <span class="menuitem"><strong>Advanced</strong>...</span>, and in the sheet that appears, click the <span class="menuitem">Hardware</span>, <span class="menuitem">Ethernet</span>, or <span class="menuitem">AirPort</span> tab.</li>
</ol>
Look at the bottom of the window for “Wi-Fi Address”, the hexadecimal characters next to this are the machines MAC address.

<!--more-->
<h2><b>USING TERMINAL</b></h2>
<div class="article_body">
<div class="article_body_table">
<div class="article_body_row">
<div></div>
<div id="article_body_column_center"><b>Step One</b>
Launch <i>Terminal</i> from the Applications:Utilities folder.</div>
<div><b>Step Two</b>
Type <strong><i>ifconfig</i></strong> into the Terminal window and press the Enter/Return key.</div>
<div></div>
<div><b>Step Three</b>
You will be shown data on all your interfaces.On systems with both an <strong>ethernet and wireless connection</strong>,

</div>
<div>

<strong>en0 will be your ethernet interface</strong> and <strong>en1 will be your wireless interface.</strong> A MAC address will be shown for both <strong>en0</strong> and <strong>en1</strong> and likely labeled as "ether".

<strong>On systems with just a wireless connection, en0 will be your wireless interface.</strong>

</div>
</div>
</div>
</div>
<div class="article_footer"></div>