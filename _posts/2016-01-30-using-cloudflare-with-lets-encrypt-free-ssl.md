---
id: 85
title: 'Using CloudFlare with Lets Encrypt Free  SSL'
date: 2016-01-30T19:38:11+00:00
author: Prabuddha
layout: post
guid: http://prabuddha.me/?p=85
permalink: /using-cloudflare-with-lets-encrypt-free-ssl/
dsq_thread_id:
  - "6203664044"
categories:
  - DevOps
---
Both CloudFlare and Let's Encrypt provides Free SSL to its consumer. But while CloudFlare is enabled, real IP of the site get hidden. This could create an issue with Let's Encrypt .

<strong>So how do we use both ?</strong>
<ol>
	<li>Firstly, bypass cloudflare for the site.</li>
	<li>Request cert from Let's Encrypt authority.</li>
	<li>Now reactivate cloudflare for the site.</li>
	<li>Select <strong>FULL</strong> or <strong>STRICT SSL</strong> from cloudflare SSL setting.</li>
	<li>You are DONE !</li>
</ol>
<em>Note: On Step 4 . Do not select Flexible SSL option. This would give a redirection error on the site.</em>