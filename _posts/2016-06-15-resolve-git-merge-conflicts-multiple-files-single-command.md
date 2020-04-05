---
id: 465
title: Resolve Git Merge Conflicts Multiple Files in Single command
date: 2016-06-15T21:08:29+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=465
permalink: /resolve-git-merge-conflicts-multiple-files-single-command/
dsq_thread_id:
  - "6223347011"
categories:
  - Coding
  - linux
---
<strong><em>Use Case : Merge conflict when we did git pull.</em></strong>

<em><strong>Conflict Example:</strong></em>
<pre>(Code not in Conflict)
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
(first alternative for conflict starts here/<strong>local or</strong> <strong>our version of code</strong>)
Multiple code lines here
===========
(second alternative for conflict starts here/<strong>their or remote branch version of code</strong>)
Multiple code lines here too    
&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;&lt;
(Code not in conflict here)
</pre>
<h2 id="resolving-merge-conflicts">Resolving merge conflicts while Git Pull/Push</h2>
<h3 id="find-files-with-merge-conflict">Find files with merge conflict</h3>
<em>Change working directory to project folder.</em>
<pre><em><code class="no-highlight">cd project-folder</code></em></pre>
<em>Search for all conflicting files.</em>
<pre><em><code class="no-highlight">grep -lr '&lt;&lt;&lt;&lt;&lt;&lt;&lt;' .</code></em></pre>
<em>Above will list all files which has marker special marker <code>&lt;&lt;&lt;&lt;&lt;&lt;&lt;</code> in them.</em>
<h3 id="resolve-easyobvious-conflicts">Resolve easy/obvious conflicts</h3>
<em>At this point you may review each files. If solution is to accept local/our version, run:</em>
<pre><em><code class="no-highlight">git checkout --ours PATH/FILE</code></em></pre>
<em>If solution is to accept remote/other-branch version, run:</em>
<pre><em><code class="no-highlight">git checkout --theirs PATH/FILE</code></em></pre>
&nbsp;

<strong>If you have multiple files and you want to accept local/our version, run:</strong>
<pre><code class="no-highlight">grep -lr '&lt;&lt;&lt;&lt;&lt;&lt;&lt;' . | xargs git checkout --ours</code></pre>
&nbsp;

<strong>If you have multiple files and you want to accept remote/other-branch version, run:</strong>
<pre><code class="no-highlight">grep -lr '&lt;&lt;&lt;&lt;&lt;&lt;&lt;' . | xargs git checkout --theirs</code></pre>
&nbsp;

<em>After above review your git branch using <strong>git status</strong> . And then commit and add to git folder.</em>