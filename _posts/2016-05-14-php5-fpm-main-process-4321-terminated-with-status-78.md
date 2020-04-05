---
id: 187
title: 'Fixing: init: php5-fpm main process (4321) terminated with status 78'
date: 2016-05-14T14:03:11+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=187
permalink: /php5-fpm-main-process-4321-terminated-with-status-78/
dsq_thread_id:
  - "6203573350"
categories:
  - Uncategorized
---
The process that upstart was watching died, but the pool workers are still running. To fix it, run:
<pre>#kill all php processes
sudo kill -9 $(ps aux | grep '[p]hp' | awk '{print $2}')

php5-fpm --fpm-config /etc/php5/fpm/php-fpm.conf

#or

#restart php
sudo service php5-fpm restart</pre>
&nbsp;