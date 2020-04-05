---
id: 697
title: Check zookeeper service status
date: 2018-02-08T18:06:10+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=697
permalink: /check-zookeeper-service-status/
categories:
  - DevOps
---
One other way would be to use 4 letter commands to validate if zookeeper service is healthy or not
<pre>#Tests if server is running in a non-error state. 
#The server will respond with imok if it is running. Otherwise it will not respond at all.
<strong>echo ruok | nc &lt;zookeeper ip&gt; 2181</strong>

#Lists statistics about performance and connected clients.
<strong>echo stat | nc &lt;zookeeper ip&gt; 2181</strong>

#New in 3.3.0: 
#Lists full details for the server.
<strong>echo srvr | nc localhost 2181</strong>

#New in 3.4.0: 
#Outputs a list of variables that could be used for monitoring the health of the cluster.
<strong>echo mntr | nc &lt;zookeeper ip&gt; 2181</strong>

#New in 3.4.0:
#Tests if server is running in read-only mode. 
#The server will respond with "ro" if in read-only mode or "rw" if not in read-only mode.
<strong>echo isro | nc &lt;zookeeper ip&gt; 2181

</strong>

</pre>
<div class="post-text">

More details on the documentation link below

<a href="https://zookeeper.apache.org/doc/r3.1.2/zookeeperAdmin.html#sc_zkCommands" rel="noreferrer">https://zookeeper.apache.org/doc/r3.1.2/zookeeperAdmin.html#sc_zkCommands</a>

<a href="https://zookeeper.apache.org/doc/r3.5.2-alpha/zookeeperAdmin.html">https://zookeeper.apache.org/doc/r3.5.2-alpha/zookeeperAdmin.html</a>

&nbsp;
<h2>Zookeeper process is running</h2>
<div class="post-text">

You can also use the following script to check if the server is running:
<pre>$ZK_INSTALL_DIR/bin/zkServer.sh status</pre>
</div>
</div>
<div></div>
&nbsp;
<div class="grid mb0 fw-wrap ai-start jc-end gs8 gsy">
<div class="grid--cell mr16"></div>
</div>