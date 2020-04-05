---
id: 78
title: How to get free SSL certificate for my website ?
date: 2016-01-29T22:56:54+00:00
author: Prabuddha
layout: post
guid: http://prabuddha.me/?p=78
permalink: /how-to-get-free-ssl-certificate-for-my-website/
dsq_thread_id:
  - "6221421908"
categories:
  - DevOps
---
5 years back if anyone had asked me whether  SSL is necessary ? I would have suggested him , <strong>Brother ! if you are willing to waste your hard earned money, please get a BEER ! :p </strong>

But nowadays situation has been changed. <strong>Why ? </strong>because now you could get SSL certificates from Trusted Authority at not more $0? So my today's post is dedicated to how to get free certificates for your website.

Till today's date, two best way for requesting free certificates for your websites would be :
<ol>
	<li style="padding-left: 30px; text-align: left;"><strong><a href="https://letsencrypt.org/">Let's Encrypts</a></strong></li>
	<li style="padding-left: 30px; text-align: left;"><strong><a href="https://www.cloudflare.com/" target="_blank">CloudFlare</a></strong></li>
</ol>
<p style="text-align: center;"><strong>Configuring Let's Encrypt</strong></p>
<p style="text-align: left;">Actually i am feeling too lazy to document the process of configuring Let's Encrypt. But below I'm listing some really good article that could get you to configure  LE for your site.</p>

<ol>
	<li style="text-align: left;"><a href="https://easyengine.io/tutorials/nginx/letsencrypt/">https://easyengine.io/tutorials/nginx/letsencrypt/</a></li>
	<li style="text-align: left;"><a href="https://easyengine.io/docs/lets-encrypt/">https://easyengine.io/docs/lets-encrypt/</a></li>
	<li style="text-align: left;"><a href="https://letsencrypt.org/howitworks/">https://letsencrypt.org/howitworks/</a></li>
	<li style="text-align: left;"><a href="https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-14-04">https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-14-04</a></li>
	<li style="text-align: left;"><a href="https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-14-04">https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-14-04</a></li>
</ol>
<em>Please Note, You would require sudo access to the server for configuring Let's Encrypt. So if using shared hosting please consult your SysAdmin.</em>
<p style="text-align: center;"><strong>Configuring CloudFlare</strong></p>
To get Free SSL from cloudflare, first step is to replace your current namespace with <strong>CloudFlare Namespace. </strong>Thats All ! After that you are eligible for  <strong>"Single Click SSL"</strong> from Cloudflare.

Cloudflare provide three basic type of SSL configuration : <strong>Flexible, Full, Strict.</strong>

If your base site doesn't have any preconfigured SSL certificate, you need to select <strong>Flexible SSL</strong>.
<ol>
	<li><a href="https://support.cloudflare.com/hc/en-us/articles/200170516-How-do-I-add-SSL-to-my-site-" target="_blank">https://support.cloudflare.com/hc/en-us/articles/200170516-How-do-I-add-SSL-to-my-site-</a></li>
</ol>