---
id: 151
title: Setup MailCatcher on Ubuntu 16.04
date: 2016-05-06T21:25:52+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=151
permalink: /setup-mailcatcher/
dsq_thread_id:
  - "6204162096"
image: /wp-content/uploads/2016/05/mailcatcher-test-email.png
categories:
  - DevOps
---
MailCatcher is a tool that provides an easy way for developers to inspect emails that their applications send out without having to wait for an email provider. It catches all emails that it receives, and stores them for display. Both HTML and plain text messages are supported.
<h3>Installation</h3>
<pre><code class="bash hljs"><span class="hljs-comment"># Update repositories</span>
sudo apt-get update

<span class="hljs-comment"># Install Basics</span>
<span class="hljs-comment"># build-essential needed for "make" command</span>
sudo apt-get install -y build-essential software-properties-common \
                        vim curl wget tmux

<span class="hljs-comment"># Install PHP 5.6</span>
sudo add-apt-repository -y ppa:ondrej/php
sudo apt-get update
sudo apt-get install -y php5.6-cli php5.6-fpm php5.6-mcrypt php5.6-curl

<span class="hljs-comment"># Install Mailcatcher Dependencies (sqlite, ruby)</span>
sudo apt-get install -y libsqlite3-dev ruby2.3-dev</code></pre>
And then let's install Mailcatcher .
<pre><code class="bash hljs"><span class="hljs-comment"># Install Mailcatcher as a Ruby gem</span>
<strong>sudo gem install mailcatcher</strong></code></pre>
Once that's installed, we can see the available options:
<pre><code class="bash hljs">$ <strong>mailcatcher --help</strong>
Usage: mailcatcher [options]
        --ip IP                      Set the ip address of both servers
        --smtp-ip IP                 Set the ip address of the smtp server
        --smtp-port PORT             Set the port of the smtp server
        --http-ip IP                 Set the ip address of the http server
        --http-port PORT             Set the port address of the http server
    <span class="hljs-operator">-f</span>, --foreground                 Run <span class="hljs-keyword">in</span> the foreground
    -v, --verbose                    Be more verbose
    -h, --help                       Display this <span class="hljs-built_in">help</span> information
</code></pre>
Then we can can start using Mailcatcher. We'll bind the web interface's IP address to all networks:
<pre><strong><code class="bash hljs">mailcatcher --foreground --http-ip=<span class="hljs-number">0.0</span>.<span class="hljs-number">0.0</span>
</code></strong></pre>
This will run Mailcatcher in the foreground. You can exit it by hitting Ctrl+C.
<h3>Start on Boot</h3>
It is useful to setup Mailcatcher to start when the server boots. This lets us forget about having to turn on Mailcatcher whenever we start our development machine.

Rather than install a process monitor, we can use Upstart, which currently comes out of the box with Ubuntu. This will get replaced with Systemd eventually. For now, we can use Upstart.

Create and edit file <code>/etc/init/mailcatcher.conf</code>:
<pre><code class="hljs sql">description "Mailcatcher"

<span class="hljs-operator"><span class="hljs-keyword">start</span> <span class="hljs-keyword">on</span> runlevel [<span class="hljs-number">2345</span>]
<span class="hljs-keyword">stop</span> <span class="hljs-keyword">on</span> runlevel [!<span class="hljs-number">2345</span>]

respawn

<span class="hljs-keyword">exec</span> /usr/<span class="hljs-keyword">bin</span>/env $(which mailcatcher) <span class="hljs-comment">--foreground --http-ip=0.0.0.0</span>
</span></code></pre>
Let's cover what this Upstart configuration is doing.

This configuration file tells Upstart to start Mailcatcher at runlevel 2,3,4 and 5.

Our configuration says to start when the above runlevels are reached and stop when the runlevels are absent.

The <code>respawn</code> directive tells Upstart to restart Mailcatcher if it fails.

Finally we set the command to start Mailcatcher. I used <code>/usr/bin/env</code> to find the environment's location of <code>mailcatcher</code>. This is useful as the location may change if Mailcatcher was installed using RVM or another environment manager for Ruby.

We configure Mailcatcher to run in the foreground with the <code>--foreground</code> option. Mailcatcher daemonizes by default. However, Upstart either needs to be told to <code>expect daemon</code> or to run the process in the foreground. The latter is simpler.
<pre><code class="hljs sql">sudo service mailcatcher status
sudo service mailcatcher <span class="hljs-operator"><span class="hljs-keyword">start</span>
sudo service mailcatcher restart
sudo service mailcatcher <span class="hljs-keyword">stop</span>
</span></code></pre>
Run <code>sudo service mailcatcher start</code> to kick it off.

Then you can head to the server's IP address at port <code>1080</code> to see the web interface!

&nbsp;

<img class="aligncenter wp-image-155" src="https://prabuddha.me/wp-content/uploads/2016/05/Selection_039-2.png" alt="Selection_039" width="868" height="305" />

&nbsp;