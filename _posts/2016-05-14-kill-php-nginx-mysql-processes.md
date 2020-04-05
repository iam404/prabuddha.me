---
id: 204
title: kill all PHP, Nginx, Mysql processes
date: 2016-05-14T14:13:13+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=204
permalink: /kill-php-nginx-mysql-processes/
dsq_thread_id:
  - "6308634211"
categories:
  - Uncategorized
---
<h2 id="to-kill-all-php-processes">To kill all PHP Processes</h2>
<pre>kill -9 $(ps aux | grep '[p]hp' | awk '{print $2}')</pre>
<h2 id="to-kill-all-nginx-processes">To kill all Nginx Processes</h2>
<pre>kill -9 $(ps aux | grep '[n]ginx' | awk '{print $2}')</pre>
<h2 id="to-kill-all-mysql-processes">To kill all MySQL Processes</h2>
<pre>kill -9 $(ps aux | grep '[m]ysql' | awk '{print $2}')</pre>