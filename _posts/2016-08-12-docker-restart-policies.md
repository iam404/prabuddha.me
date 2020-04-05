---
id: 456
title: Docker Restart Policies
date: 2016-08-12T12:42:49+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=456
permalink: /docker-restart-policies/
dsq_thread_id:
  - "6300890421"
categories:
  - DevOps
---
<div class="post">

Docker introduced a powerful new feature called "restart policies". Restart policies replaces the old daemon "-r" option, which itself would try to restart all previously running containers upon daemon restart, however this was rife with trouble.
<ol>
 	<li>It just flat out didn't always work, and was basically unreliable in production</li>
 	<li>Maybe you didn't actually need <em>all</em> of your containers restarted</li>
 	<li>It did not resolve links, so if a container had links and docker tried to start that container before the linked container was started, it would fail to start.</li>
 	<li>Does not apply to container crashes/stopping</li>
</ol>
With restart policies this all changes.
<ol>
 	<li>When your system boots and starts up docker, docker will reliably restart all containers that have a restart policy applied</li>
 	<li>You can specify per-container what the restart policy should be</li>
 	<li>Docker will walk link dependencies and start them in the correct order</li>
 	<li>Will monitor/restart crashed containers</li>
</ol>
<h3>Usage</h3>
<div class="highlight">
<pre><code class="language-bash" data-lang="bash">docker run -d --restart always --name myredis redis
docker run -d --restart always --link myredis:redisdb myapp
</code></pre>
</div>
Other restart policy modes are:
<ul>
 	<li>no - no restart policy</li>
 	<li>on-failure - restart if exit code is not 0</li>
</ul>
Docker will also back-off on restarts if they are too frequent, and when using "on-failure" you can set the max restarts.

On minimal distros such as boot2docker, restart policies can be used in place of an init system... I'd even argue that if you are containerizing everything it should always replace an init system.

Basically, if you aren't using restart policies you are doing it wrong. If you aren't using restart=always on your long running processes you are probably also doing it wrong.

</div>
&nbsp;

Reference: <a href="https://docs.docker.com/engine/admin/start-containers-automatically/#use-a-process-manager">https://docs.docker.com/engine/admin/start-containers-automatically/#use-a-process-manager</a>

&nbsp;
<div class="related"></div>