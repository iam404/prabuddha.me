---
id: 523
title: Edit the last local Git commit message
date: 2016-03-01T10:26:06+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=523
permalink: /edit-last-local-git-commit-message/
categories:
  - git
  - linux
---
When you want to edit the last local commit message:
<blockquote>
<pre>git commit --amend</pre>
</blockquote>
Make your changes then save and quit VIM with:
<blockquote>
<pre>:wq</pre>
</blockquote>
Or

to re write the whole commit message in single command:
<blockquote>
<pre>git commit --amend -m "New commit message"</pre>
</blockquote>
&nbsp;

Push to branch .