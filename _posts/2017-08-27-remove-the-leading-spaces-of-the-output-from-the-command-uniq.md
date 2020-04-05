---
id: 863
title: Remove the leading spaces of the output from the command “uniq”
date: 2017-08-27T14:57:56+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=863
permalink: /remove-the-leading-spaces-of-the-output-from-the-command-uniq/
categories:
  - DevOps
---
If you want to remove the leading spaces, just pipe the output to <code>sed</code> to do so:
<pre><code>uniq -c file | sed 's/^\s*//'
</code></pre>
Example:
<pre><code>$ uniq -c file
      2 00
      2 01
      2 00
      2 02
      1 03
      3 aa
$ uniq -c file | sed 's/^\s*//'
2 00
2 01
2 00
2 02
1 03
3 aa</code></pre>