---
id: 47
title: '[Fix mysql Error] InnoDB: Error: unable to create temporary file'
date: 2016-01-26T13:48:30+00:00
author: Prabuddha
layout: post
guid: http://prabuddha.me/?p=47
permalink: /fix-mariadb-error-innodb-error-unable-create-temporary-file/
dsq_thread_id:
  - "6203718940"
categories:
  - DevOps
---
Fixing mysql error could be troublesome. One of the recent issue i faced with mariadb after reboot of server is :
<pre>160122 1:49:55 [Note] InnoDB: Using Linux native AIO
160122 1:49:55 [Note] InnoDB: Using CPU crc32 instructions
160122 1:49:55 [ERROR] mysqld: Can't create/write to file '/run/mysqld/tmp/ibvsXeo0' (Errcode: 2 "No such file or directory")
2016-01-22 01:49:55 7ff3031bd780 InnoDB: Error: unable to create temporary file; errno: 2
160122 1:49:55 [ERROR] Plugin 'InnoDB' init function returned error.
160122 1:49:55 [ERROR] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.
160122 1:49:55 [Note] Plugin 'FEEDBACK' is disabled.
160122 1:49:55 [ERROR] Unknown/unsupported storage engine: InnoDB
160122 1:49:55 [ERROR] Aborting</pre>
The reason for this error is, MySQL server is not able to access your /tmp directory or "tmpdir" PATH set in your my.cnf , to write and create temporary files. Make sure /tmp directory exist and is owned by mysql user . Type the following commands to fix this error. You must be login as root user and type:
<pre># grep your my.cnf file for folder path set as tmpdir

grep "tmpdir"  /etc/mysql/my.cnf</pre>
<pre class="programlisting">#sample output

[mysqld]
tmpdir=/run/mysqld/tmp</pre>
<pre>#make sure above folder path exist. or create if doesn't exist
#set mysql user permission to the folder

<code>chown -R mysql:root tmp/

chmod 755 tmp/

#Restart MYSQL to test

service mysql restart</code></pre>
Now, this should fix your issue and  mysql server should start without a problem.