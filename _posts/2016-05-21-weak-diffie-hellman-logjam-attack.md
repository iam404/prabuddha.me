---
id: 45
title: Fix for Weak Diffie-Hellman and the Logjam Attack
date: 2016-05-21T12:04:52+00:00
author: Prabuddha
layout: post
guid: http://prabuddha.me/?p=45
permalink: /weak-diffie-hellman-logjam-attack/
dsq_thread_id:
  - "6239827199"
categories:
  - Uncategorized
---
Diffie-Hellman key exchange is an popular cryptographic algorithm that provide secure way of exchanging cryptographic key ever a public network/channel. D–H is one of the earliest practical examples of public key exchange implemented within the field of cryptography. It is fundamental to many protocols including HTTPS, SSH, IPsec, SMTPS, and protocols that rely on TLS.

Below are several weaknesses in how Diffie-Hellman key exchange has been deployed:
<ol class="mainpt">
 	<li><b>Logjam attack against the TLS protocol.</b> The Logjam attack allows a man-in-the-middle attacker to downgrade vulnerable TLS connections to 512-bit export-grade cryptography. This allows the attacker to read and modify any data passed over the connection. The attack is reminiscent of the <a href="http://freakattack.com">FREAK attack</a>, but is due to a flaw in the TLS protocol rather than an implementation vulnerability, and attacks a Diffie-Hellman key exchange rather than an RSA key exchange.</li>
 	<li>Millions of HTTPS, SSH, and VPN servers all use the same prime numbers for Diffie-Hellman key exchange. Practitioners believed this was safe as long as new key exchange messages were generated for every connection. However, the first step in the number field sieve—the most efficient algorithm for breaking a Diffie-Hellman connection—is dependent only on this prime. After this first step, an attacker can quickly break individual connections.</li>
</ol>
To fix issue with weak Diffie Hellman Logjam Attack you need to:
<ol>
 	<li><strong>Disable Export Cipher Suites.</strong></li>
 	<li><strong>Deploy (Ephemeral) Elliptic-Curve Diffie-Hellman (ECDHE)</strong></li>
 	<li><strong>Use a Strong Diffie Hellman Group</strong></li>
</ol>
<h3><strong>Below is a Fix for Nginx web server:</strong></h3>
Update strong <code>ssl_ciphers</code> in global NGINX configuration file
<pre>vim /etc/nginx/nginx.conf
</pre>
<pre>ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:ECDHE-RSA-DES-CBC3-SHA:ECDHE-ECDSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA;
</pre>
<h3 id="using-a-strong-dh-group"><strong>Create a Strong DH Group</strong></h3>
Run following command on your server
<pre>openssl dhparam -out /etc/nginx/dhparams.pem 2048
</pre>
open your NGINX configuration file
<pre>vim /etc/nginx/nginx.conf
</pre>
and update below directive into it
<pre>ssl_dhparam /etc/nginx/dhparams.pem;
</pre>
<strong>Reload</strong> or <strong>Restart</strong> Nginx Services.
<pre>service nginx restart</pre>
<h2>References:</h2>
<ul>
 	<li><a href="https://weakdh.org/sysadmin.html">https://weakdh.org/sysadmin.html</a></li>
</ul>