---
id: 519
title: How to Rename a local and remote branch in git
date: 2016-09-01T10:02:45+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=519
permalink: /rename-local-remote-branch-git/
categories:
  - Coding
  - DevOps
  - git
  - Uncategorized
---
Just in case you pushed a wrongly named branch. *wink*

<strong>1. Rename your local branch.</strong>
Get to the branch you want to rename:
<div>
<div id="highlighter_269078" class="syntaxhighlighter plain">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">git branch -m new-name</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<strong>2. Delete the old-name remote branch and push the new-name local branch.</strong>
<div>
<div id="highlighter_850321" class="syntaxhighlighter plain">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">git push origin :old-name new-name</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<strong>3. Reset the upstream branch for the new-name local branch.</strong>
Switch to the branch and then:
<div>
<div id="highlighter_136715" class="syntaxhighlighter plain">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">git push origin -u new-name</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<em><strong>Note:</strong> Make sure you run command <code>git status</code> and check that newly created branch is pointing to its own ref and not older one. If you find the reference to older branch, you need to unset the upstream using</em>
<pre><code>git branch --unset-upstream</code></pre>