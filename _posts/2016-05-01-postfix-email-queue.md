---
id: 125
title: 'Inspecting Postfix&#8217;s email queue'
date: 2016-05-01T19:40:06+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=125
permalink: /postfix-email-queue/
dsq_thread_id:
  - "6257677690"
categories:
  - linux
---
This post explains how to view messages in the postfix queue.

Postfix maintains two queues, the pending mails queue, and the deferred mail queue, the differed mail queue has the mail that has soft-fail and should be retried .Postfix retries the deferred queue on set intervals (configurable, and by default 5 minutes)

In any case, the following commands should be useful
<h2>1- Display a list of queued mail, deferred and pending</h2>
<pre>mailq</pre>
or
<pre>postqueue -p</pre>
To save the output to a text file you can run
<pre>mailq &gt; myfile.txt</pre>
or
<pre>postqueue -p &gt; myfile.txt</pre>
the above commands display all queued messages (Not the message itself but the sender and recipients and ID), The ID is particularly useful if you want to inspect the message itself.
<h2>2- View message (contents, header and body) in Postfix queue</h2>
Assuming the message has the ID XXXXXXX (you can see the ID form the QUEUE)
<pre>postcat -vq XXXXXXXXXX</pre>
Or to save it in a file
<pre>postcat -vq XXXXXXXXXX &gt; themessage.txt</pre>
<h2>3- Tell Postfix to process the Queue now</h2>
<pre>postqueue -f</pre>
OR
<pre>postfix flush</pre>
<h2>4- Delete queued mail</h2>
Delete all queued mail
<pre>postsuper -d ALL</pre>
Delete differed mail queue messages

(The ones the system intends to retry later)
<pre>postsuper -d ALL deferred</pre>
&nbsp;