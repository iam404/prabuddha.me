---
id: 31
title: Configure WordPress with Redis object + page caching
date: 2016-05-18T11:53:30+00:00
author: Prabuddha
layout: post
guid: http://prabuddha.me/?p=31
permalink: /configure-wordpress-with-redis-cache/
dsq_thread_id:
  - "6235404436"
image: /wp-content/uploads/2016/01/img-redis.jpg
categories:
  - DevOps
---
&nbsp;

Redis is an open source, in-memory, key value data store, used as database, cache and message broker. One of the easy way to configure Redis with Wordpress + Nginx, is to use <a href="https://easyengine.io/" target="_blank">EasyEngine</a> . But below lets discuss about geek part and configure redis object + page caching manually  on server.
<h3><strong>Step 1: Install Redis-Server</strong></h3>
<pre class="no-highlight">add-apt-repository ppa:chris-lea/redis-server</pre>
<pre class="no-highlight">sudo apt-get update</pre>
<pre class="code-pre "><code>sudo apt-get install redis-server php5-redis</code></pre>
<h3><strong>Step 2: Test Redis  Setup</strong></h3>
<pre class="no-highlight">root@example.com:~# redis-cli ping
PONG</pre>
This should print a `<code>PONG`</code> as the response.
<h3><strong>Step 3: Configure Redis configuration</strong></h3>
<pre class="code-pre "><code>sudo nano /etc/redis/redis.conf</code></pre>
Uncomment or Add following line at top
<pre># By default Redis listens for connections from all the network interfaces
# available on the server. It is possible to listen to just one or multiple
# interfaces using the "bind" configuration directive, followed by one or
# more IP addresses.
# Examples:
# bind 192.168.1.100 10.0.0.1

<span style="color: #ff0000;">bind </span><span style="color: #ff0000;">127</span><span style="color: #ff0000;">.0.0.1</span></pre>
Add these lines at the end of the file:
<pre class="code-pre "><code>maxmemory 256mb
maxmemory-policy volatile-lru
</code></pre>
<h3>Step 4 : Restart redis server</h3>
<pre>sudo service redis-server restart</pre>
<h3 id="step-4-—-enable-cache-settings-in-wp-config-php">Step 5 : Enable Cache Settings in wp-config.php</h3>
Next, edit the <code>wp-config.php</code> file to add a cache key salt with the name of your site.
<pre class="code-pre "><code>nano /var/www/example.com/wp-config.php
</code></pre>
Add this line at the end of the  <code>* Authentication Unique Keys and Salts.</code> section:
<pre class="code-pre "><code>define('WP_CACHE_KEY_SALT', '<span class="highlight">example.com</span>');
</code></pre>
You can use your domain name or another string as the salt.
<h3 id="step-4-—-enable-cache-settings-in-wp-config-php">Step 6: Restart web services</h3>
<pre class="code-pre "><code>sudo service nginx restart</code></pre>
<pre>sudo service php5-fpm restart</pre>
<h3>Step 7: Install &amp; configure Object Cache plugin</h3>
Install and Activate below wordpress plugin for object cache:
<ul>
	<li>
<h3>   <a href="https://wordpress.org/plugins/redis-cache/" target="_blank">Redis Object Cache</a></h3>
Enable the object cache under <em>Tools -&gt; Redis</em>.</li>
</ul>
<h2 id="monitor-redis-with-redis-cli">Monitor Redis with redis-cli</h2>
To monitor Redis, use the <code>redis-cli</code> command like so:
<pre class="code-pre "><code>redis-cli monitor
</code></pre>
When you run this command, you will see the real-time output of Redis serving cached queries. If you don't see anything, visit your website and reload a page.
<h2>Clear all Redis cache</h2>
The Redis cache can be purged by using the <code>flushall</code> command from the Redis command line:
<pre class="code-pre "><code>redis-cli flushall

</code></pre>
&nbsp;

OTHER RESOURCES :
<ol>
	<li><a href="http://redis.io/documentation">http://redis.io/documentation</a></li>
	<li><a href="http://redis.io/commands">http://redis.io/commands</a></li>
	<li><a href="https://wordpress.org/plugins/nginx-helper/">https://wordpress.org/plugins/nginx-helper/</a></li>
</ol>