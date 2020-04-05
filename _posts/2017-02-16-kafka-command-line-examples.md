---
id: 349
title: Some Kafka Command line Examples
date: 2017-02-16T09:15:24+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=349
permalink: /kafka-command-line-examples/
dsq_thread_id:
  - "6209312592"
categories:
  - DevOps
---
You may download latest kafka source from <a href="https://kafka.apache.org/downloads">https://kafka.apache.org/downloads</a> .

Kafka command-line tools are located in <samp class="ph codeph"></samp><code>bin/</code> of source directory.
<ul>
 	<li><samp class="ph codeph">kafka-topics.sh</samp></li>
</ul>
Create, alter, list, and describe topics. For example.
<blockquote>
<pre class="pre codeblock hljs"># List topics
$ ./bin/kafka-topics.sh --zookeeper zk.example.com:2181 --list
test-topic
test

# Create a new topics
$ ./bin/kafka-topics.sh --create --zookeeper zk.example.com:2181 --replication-factor 2 
  --partitions 1 --topic topicname 

# Describe a topic
$ ./bin/kafka-topics.sh --zookeeper zk.example.com:2181 --describe --topic topicname</pre>
</blockquote>
&nbsp;
<ul>
 	<li><samp class="ph codeph">kafka-console-consumer.sh</samp></li>
</ul>
Read data from a Kafka topic and write it to standard output. For example.
<blockquote>
<pre class="pre codeblock hljs">$ ./bin/kafka-console-consumer.sh --zookeeper zk.example.com:2181 --topic test-topic</pre>
</blockquote>
Note: To consumes events from beginning can append <code>--from-beginning</code> .

&nbsp;
<ul>
 	<li>kafka-console-producer.sh</li>
</ul>
Read data from standard output and write it to a Kafka topic. For example.
<blockquote>
<pre class="pre codeblock hljs">$ ./bin/kafka-console-producer.sh --broker-list kafka.example.com:9092,kafka03.example.com:9092 --topic test-topic</pre>
</blockquote>
&nbsp;

&nbsp;