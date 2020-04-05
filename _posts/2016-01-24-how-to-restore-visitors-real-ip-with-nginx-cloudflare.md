---
id: 59
title: How to restore visitors real IP with Nginx-Cloudflare ?
date: 2016-01-24T13:48:47+00:00
author: Prabuddha
layout: post
guid: http://prabuddha.me/?p=59
permalink: /how-to-restore-visitors-real-ip-with-nginx-cloudflare/
dsq_thread_id:
  - "6210717397"
categories:
  - DevOps
---
Since CloudFlare acts as a reverse proxy, all connections now come from one of CloudFlare's IP addresses. CloudFlare follows industry standards and includes the originating IP address in the X-Forwarded-For header. The CF-Connecting-IP header may also be used. To preserve the originating IP of your visitor, use the following Real IP <a href="http://nginx.org/en/docs/http/ngx_http_realip_module.html">Nginx module</a> .

<a href="http://nginx.org/en/docs/http/ngx_http_realip_module.html">http://nginx.org/en/docs/http/ngx_http_realip_module.html</a>

Find <strong>nginx.conf</strong> configuration file :
<pre class="wp-code-highlight prettyprint prettyprinted"><span class="com"># locate nginx.conf</span></pre>
Add below code in <b>nginx.conf</b> under <b>http</b> section:
<pre><span class="line"><span class="comment"># CloudFlare</span></span>
<span class="line"><span class="comment"># https://www.cloudflare.com/ips-v4</span></span>
<span class="line"><span class="comment"># https://www.cloudflare.com/ips-v6</span></span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">204.93</span>.<span class="number">240.0</span>/<span class="number">24</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">204.93</span>.<span class="number">177.0</span>/<span class="number">24</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">199.27</span>.<span class="number">128.0</span>/<span class="number">21</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">173.245</span>.<span class="number">48.0</span>/<span class="number">20</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">103.21</span>.<span class="number">244.0</span>/<span class="number">22</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">103.22</span>.<span class="number">200.0</span>/<span class="number">22</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">103.31</span>.<span class="number">4.0</span>/<span class="number">22</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">141.101</span>.<span class="number">64.0</span>/<span class="number">18</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">108.162</span>.<span class="number">192.0</span>/<span class="number">18</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">190.93</span>.<span class="number">240.0</span>/<span class="number">20</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">188.114</span>.<span class="number">96.0</span>/<span class="number">20</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">197.234</span>.<span class="number">240.0</span>/<span class="number">22</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">198.41</span>.<span class="number">128.0</span>/<span class="number">17</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">2400</span>:cb00::/<span class="number">32</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">2606</span>:<span class="number">4700</span>::/<span class="number">32</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">2803</span>:f800::/<span class="number">32</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">2405</span>:b500::/<span class="number">32</span>;</span>
<span class="line"><span class="built_in">set</span>_real_ip_from <span class="number">2405</span>:<span class="number">8100</span>::/<span class="number">32</span>;</span>
<span class="line">real_ip_header CF-Connecting-IP;</span></pre>
Now save changes and restart your nginx server
<pre class="wp-code-highlight prettyprint prettyprinted"><span class="pln">service nginx restart</span></pre>
That is all, now nginx will be able to log real ip address of the visitor.