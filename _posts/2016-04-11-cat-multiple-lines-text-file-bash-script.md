---
id: 592
title: Cat Multiple Lines of Text to a file from bash script
date: 2016-04-11T17:39:05+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=592
permalink: /cat-multiple-lines-text-file-bash-script/
dsq_thread_id:
  - "6407137545"
categories:
  - linux
tags:
  - bash
  - cat
  - echo
  - multilines
---
You may find yourself needing to echo out multiple lines to the console, or even to another file, then you’ll want to use the following method which is much cleaner and much more efficient.

It’s very useful in creating another document, script, or file without having to use echo for each line.

&nbsp;
<pre>cat &gt; new_filename &lt;&lt;EOL
This is a test
This is a test
This is a test
This is a test
This is a test
EOL</pre>
&nbsp;

You may also put variables to the multiline string.

<strong>Cat multiline string with variable expansion</strong>
<div id="crayon-5a57a35e4e1a7048047768-15" class="crayon-line"></div>
<pre id="crayon-5a57a35e4e1a7048047768-15" class="crayon-line"><span class="crayon-v">variable1="ONE"
variable2="TWO"
variable3="THREE"
 
cat &gt; myfile.txt &lt;&lt;EOF
this file has 3 variables
$variable1
$variable2
$variable3
EOF</span></pre>