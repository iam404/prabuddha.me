---
id: 114
title: How to disable discrete graphics card in fedora ?
date: 2016-04-22T22:47:19+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=114
permalink: /disable-nvidia-in-fedora/
dsq_thread_id:
  - "6205138270"
categories:
  - linux
---
Whenever Linux and nVidia Optimus technology comes into picture, there is always pain in that. When I switched to Linux from windows on my optimus enabled laptop, I found lot of power consumption and heat dissipation.

Only way to stop that was to disable discrete graphics card. After wasting time on that for hours, I finally found the workaround to do that.

Disabling the discrete nvidia graphics card

1. Get the source

Download bbswitch source code from <span style="color: #0000ff;"><a style="color: #0000ff;" href="https://github.com/Bumblebee-Project/bbswitch/downloads" rel="nofollow">https://github.com/Bumblebee-Project/bbswitch/downloads</a></span>

2. Unpack the archive
<table style="height: 35px;" border="0" width="674" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><span style="color: #0000ff;"><code class="bash functions">gzip</code> <code class="bash plain">-c bbswitch-x.y.z.</code><code class="bash functions">tar</code><code class="bash plain">.gz | </code><code class="bash functions">tar</code> <code class="bash plain">xf -</code></span></div>
</div></td>
</tr>
</tbody>
</table>
<div>
<div id="highlighter_983696" class="syntaxhighlighter bash">3. Install dkms if you dont already have it</div>
<div class="syntaxhighlighter bash"></div>
<div class="syntaxhighlighter bash">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><span style="color: #0000ff;"><code class="bash functions">sudo</code> <code class="bash plain">yum </code><code class="bash functions">install</code> <code class="bash plain">dkms</code></span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<div>
<div id="highlighter_50889" class="syntaxhighlighter bash">4. Change to the source directory</div>
</div>
<div class="syntaxhighlighter bash"></div>
<div class="syntaxhighlighter bash">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><span style="color: #0000ff;"><code class="bash functions">cd</code> <code class="bash plain">bbswitch-x.y.z</code></span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
<div>
<div id="highlighter_297083" class="syntaxhighlighter bash">5. Compile and install bbswitch</div>
<div class="syntaxhighlighter bash"></div>
</div>
<div class="syntaxhighlighter bash">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><span style="color: #0000ff;"><code class="bash functions">make</code> <code class="bash plain">-f Makefile.dkms</code></span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
<div>
<div id="highlighter_520573" class="syntaxhighlighter bash">6. Unload the nouveau driver</div>
</div>
<div class="syntaxhighlighter bash"></div>
<div class="syntaxhighlighter bash">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><span style="color: #0000ff;"><code class="bash plain">modprobe -r nouveau</code></span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
<div>
<div id="highlighter_755986" class="syntaxhighlighter bash">7. Load bbswitch</div>
<div class="syntaxhighlighter bash"></div>
</div>
<div class="syntaxhighlighter bash">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><span style="color: #0000ff;"><code class="bash plain">modprobe bbswitch</code></span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
<div>
<div id="highlighter_600323" class="syntaxhighlighter bash">8. Switch off the card</div>
</div>
<div class="syntaxhighlighter bash"></div>
<div class="syntaxhighlighter bash">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><span style="color: #0000ff;"><code class="bash functions">tee</code> <code class="bash plain">/proc/acpi/bbswitch</code> <code class="bash plain">&lt;&lt;&lt; OFF</code></span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
<div>
<div id="highlighter_324419" class="syntaxhighlighter bash">Now you are done switching off the graphics card,</div>
</div>
To Get the status of the card
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><span style="color: #0000ff;"><code class="bash functions">cat</code> <code class="bash plain">/proc/acpi/bbswitch</code></span></div>
</div></td>
</tr>
</tbody>
</table>
The output should be like this :
<div>
<div id="highlighter_155957" class="syntaxhighlighter bash">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter"><span style="color: #0000ff;">  0000:01:00.0 OFF</span></td>
<td class="code"></td>
</tr>
</tbody>
</table>
</div>
</div>
That's it!

Now you may want to automate the above steps on startup. To automate this on startup add the following lines in <span style="color: #0000ff;"><code>/etc/rc.d/rc.local</code></span>
<pre><span style="color: #0000ff;">#!/bin/sh</span>
<span style="color: #0000ff;">#Turn off the nvidia graphics card</span>
<span style="color: #0000ff;">modprobe -r nouveau</span>
<span style="color: #0000ff;">modprobe bbswitch</span>
<span style="color: #0000ff;">tee /proc/acpi/bbswitch &lt;&lt;&lt; OFF</span></pre>
Note: To turn on the device :
<pre><span style="color: #0000ff;">tee /proc/acpi/bbswitch &lt;&lt;&lt; ON</span></pre>
&nbsp;