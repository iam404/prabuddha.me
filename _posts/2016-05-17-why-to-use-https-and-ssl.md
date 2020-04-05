---
id: 43
title: Why you need to move from HTTP to HTTPS/SSL ?
date: 2016-05-17T21:09:46+00:00
author: Prabuddha
layout: post
guid: http://prabuddha.me/?p=43
permalink: /why-to-use-https-and-ssl/
dsq_thread_id:
  - "6371910472"
image: /wp-content/uploads/2016/01/https1.png
categories:
  - DevOps
---
HTTPS, also referred as <strong>HTTP Secure </strong>or <strong>HTTP over SSL, </strong>is a protocol widely used over computer network for secure communication.

<strong>Should I Use HTTPS?</strong>

A couple of years ago I would have said no, I mean why would you need HTTPS that cost fortune ? However, today I have reasons that it’s necessary to have it and here’s why.
<ul class="top-list">
	<li>To a certain degree encrypted connections make up for the inadequacy of the already insecure DNS infrastructure, as with HTTP you don’t get the same level of security.</li>
	<li>When browsers are forced to use HTTPS connections it slightly raises the security policy, which means less opportunities for hackers on the client side regardless of the communication channel.</li>
</ul>
Let’s take Facebook for example, a few years ago they used to server their login page over HTTP. A government in the middle-east decided to inject JavaScript into the page to steal user’s passwords straight from the login form. It didn’t matter the password were sent to Facebook via HTTPS, the login page was running on HTTP which led to accounts being compromised.
<ul>
	<li><strong>Authenticity</strong></li>
</ul>
Personally I would say this is the major benefit of sending data via HTTPS as it tells the end user that the content delivered is from the source and it hasn’t been tampered with in any way.
<ul>
	<li><strong>Confidentiality</strong></li>
</ul>
The webpages you’re viewing and what you’re doing aren’t visible to anyone sniffing network traffic as it’s fully encrypted via HTTPS.
<ul>
	<li><strong>Protection</strong></li>
</ul>
Protection is important when it comes to handle money transfers like with online banking or for e-commerce sites, so you definitely don’t want anyone malicious to send another copy of commands and transfer twice.
<h4>Google also loves security and would like to establish secure environment over the internet, therefore <span class="qlink_container">Google starts giving ranking advantage</span> in search engine to secure HTTPS websites.</h4>
<h2><strong>Conclusion</strong></h2>
When most people think of a secure connection they take all above points into consideration, but I would say that authenticity is the most vital. Let’s say I go to quora.com, what I expect is exactly what quora sent, not anything else. I don’t really care if anyone sees what I’m reading, but I am concerned if there’s a man in the middle feeding me false content and injecting code for an attack.

The real question is: <strong>Why isn’t everyone using HTTPS?</strong> In short, it’s not the default configuration yet, but we’re slowly getting there.
<h2>Some free SSL provider</h2>
<ol>
	<li><a href="https://www.cloudflare.com/">https://www.cloudflare.com/</a></li>
	<li><a href="https://letsencrypt.org/">https://letsencrypt.org/</a></li>
	<li><a href="https://www.symantec.com/">https://www.symantec.com/ </a> (30 days free Trial)</li>
	<li><a href="https://ssl.comodo.com/">https://ssl.comodo.com/</a>  (90 days free Trial)</li>
	<li><a href="https://www.freessl.com/">https://www.freessl.com/</a> (30 days free Trial)</li>
	<li><a href="http://www.cacert.org/">http://www.cacert.org/</a></li>
</ol>