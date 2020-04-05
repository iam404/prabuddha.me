---
id: 958
title: MacOSX Python multiprocessing crash with fork error
date: 2019-04-24T19:49:08+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=958
permalink: /macosx-python-multiprocessing-crash-with-fork-error/
categories:
  - DevOps
---
Error:
<pre>objc[67390]: +[__NSPlaceholderDate initialize] may have been in progress in another thread when fork() was called.
objc[67390]: +[__NSPlaceholderDate initialize] may have been in progress in another thread when fork() was called. We cannot safely call it or ignore it in the fork() child process. Crashing instead. Set a breakpoint on objc_initializeAfterForkError to debug.</pre>
&nbsp;

Fix:

This error occurs because of added security to restrict multithreading in Mac OSX since version High Sierra.

To fix the issue Set an environment variable .bash_profile to allow multithreading applications or scripts under the new Mac OS security rules.

Open a terminal:
<pre class="lang-py prettyprint prettyprinted"><code><span class="pln">$ nano </span><span class="pun">.</span><span class="pln">bash_profile</span></code></pre>
Add the following line to the end of the file:
<pre class="lang-py prettyprint prettyprinted"><code><span class="pln">export OBJC_DISABLE_INITIALIZE_FORK_SAFETY</span><span class="pun">=</span><span class="pln">YES</span></code></pre>
Save, exit, close terminal and re-open the terminal. Check to see that the environment variable is now set. You should now be able to run your python script with multithreading.
<div class="grid mb0 fw-wrap ai-start jc-end gs8 gsy"><time datetime="2018-09-07T22:44:22"></time>
<div class="grid--cell mr16"></div>
</div>
&nbsp;