---
id: 212
title: 'error: The following untracked working tree files would be overwritten by merge'
date: 2016-03-19T09:47:02+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=212
permalink: /git-error-untracked-working-tree-files/
dsq_thread_id:
  - "6202903820"
categories:
  - Uncategorized
---
GIT Issue. :
<blockquote>root@vagrant:~/testfolder$ git pull
Updating a1e21e5..1d34553
error: The following untracked working tree files would be overwritten by merge:
page.py
single-download.py
Please move or remove them before you can merge.
Aborting</blockquote>
To Fix solution is actually simple :
<pre>git clean  -d  -fx ""
</pre>
<ul>
 	<li>X - delete ignore file has not identified for git files</li>
 	<li>D -- deletion was not added to the git in the path of the file</li>
 	<li>F - forced operation</li>
</ul>
&nbsp;