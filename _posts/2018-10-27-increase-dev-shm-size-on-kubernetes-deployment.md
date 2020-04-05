---
id: 827
title: 'Increase &#8220;/dev/shm&#8221; size on kubernetes deployment'
date: 2018-10-27T22:08:22+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=827
permalink: /increase-dev-shm-size-on-kubernetes-deployment/
categories:
  - DevOps
---
Recently we came across an issue, where our application running inside Kubernetes pod was running out of "shm" memory. From there we came to know Kubernetes default "shm" limit is 64mb irrespective of the size of nodes.
<h3>Fix</h3>
Until now there is no k8s configurable way to increase that.  But can use the hack provided here -  https://docs.okd.io/latest/dev_guide/shared_memory.html

Mounting an <code>emptyDir</code> to  "/dev/shm" and setting the medium to <code>Memory</code> did the trick!
<div class="post-text">
<pre>spec:
  volumes:
  - name: dshm
    emptyDir:
      <strong>medium: Memory</strong>
  containers:
  - image: imagename:latest
    <strong>volumeMounts:
      - mountPath: /dev/shm
        name: dshm
</strong></pre>
</div>
<div class="grid--cell mr16"></div>
<div>Adding above mount point works but it will use the complete memory as tmpfs. But there is an open GitHub pull request that may give us the option for limiting the tmpfs size - <a href="https://github.com/kubernetes/kubernetes/pull/63641">https://github.com/kubernetes/kubernetes/pull/63641</a></div>
&nbsp;