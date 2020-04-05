---
id: 588
title: Send Email Attachment from Ubuntu Command Line
date: 2018-01-10T22:29:43+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=588
permalink: /send-email-attachment-ubuntu-command-line/
dsq_thread_id:
  - "6405320798"
categories:
  - Uncategorized
---
The ability to send e-mail alerts is essential for the day to day management of any VPS.
<h3>Install mailutils</h3>
<pre># For ubuntu/debian based distro 
$ sudo apt-get install mailutils</pre>
Now its time to send an email attachment using mail command a shown.
<pre>$ echo "Put Message Body Here" | mail -s "Put Subject Here" user@example.com -A attachmentfile.zip</pre>
In the above command:
<ul>
 	<li><code>-s</code> – specifies the message subject.</li>
 	<li><code>-A</code> – helps to attach a file.</li>
</ul>
<em>Note: You would need postfix or </em>ssmtp<em> to be already installed to deliever mail to outside world.</em>