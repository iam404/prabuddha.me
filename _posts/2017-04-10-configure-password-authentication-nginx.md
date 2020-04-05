---
id: 571
title: Configure Password Authentication with Nginx
date: 2017-04-10T14:48:49+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=571
permalink: /configure-password-authentication-nginx/
dsq_thread_id:
  - "6404488112"
categories:
  - DevOps
  - security
tags:
  - authentication
  - easyengine
  - nginx
---
<h2>Add a new user for HTTP Auth .</h2>
<pre>printf "user:$(openssl passwd -crypt password 2&gt; /dev/null)\n" &gt; /etc/nginx/htpasswd</pre>
Replace <code>user</code> with user want to create and <code>password</code> with the password of the user want to set.

Create an <code>acl.conf</code> file.
<pre># HTTP authentication || IP address
satisfy any;
auth_basic "Restricted Area";
auth_basic_user_file htpasswd;
# Allowed IP Address List
allow 127.0.0.1;
deny all;</pre>
Place the acl.conf file in the nginx root directory.
<h2>Enable httpauth from the site config</h2>
<pre class="code-pre ">server {
    listen 80 ;
    ... 
    ...

# Include acl.conf file at the end
    <strong>include acl.conf;
</strong>}
</pre>
<strong>Include acl.conf from the site config to enable.</strong>

<strong>Restart </strong>nginx<strong> to reflect the change.</strong>
<h2 id="confirm-the-password-authentication">Confirm the Password Authentication</h2>
To confirm that your content is protected, try to access your restricted content in a web browser. You should be presented with a username and password prompt that looks like this:

<img class="aligncenter" src="https://assets.digitalocean.com/articles/nginx_password_1404/password_prompt.png" alt="Nginx password prompt" />

If you enter the correct credentials, you will be allowed to access the content. If you enter the wrong credentials or hit "Cancel", you will see the "Authorization Required" error page:

<img class="aligncenter" src="https://assets.digitalocean.com/articles/nginx_password_1404/unauthorized_error.png" alt="Nginx unauthorized error" />