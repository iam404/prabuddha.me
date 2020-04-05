---
id: 567
title: 'locale.Error: unsupported locale setting'
date: 2017-01-09T12:25:09+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=567
permalink: /locale-error-unsupported-locale-setting/
dsq_thread_id:
  - "6404085048"
categories:
  - Uncategorized
---
Fix python unsupported locale error in Debian based distro.

Error :
<pre>ubuntu@:~$ sudo pip3 install boto3
Traceback (most recent call last):
 File "/usr/bin/pip3", line 11, in &lt;module&gt;
 sys.exit(main())
 File "/usr/lib/python3/dist-packages/pip/__init__.py", line 215, in main
 locale.setlocale(locale.LC_ALL, '')
 File "/usr/lib/python3.5/locale.py", line 594, in setlocale
 return _setlocale(category, locale)
locale.Error: unsupported locale setting</pre>
<strong>To Fix </strong><strong>:</strong>
<pre class="lang-py prettyprint prettyprinted"><span class="pln">export LC_ALL</span><span class="pun">=</span><span class="str">"en_US.UTF-8"</span><span class="pln">
export LC_CTYPE</span><span class="pun">=</span><span class="str">"en_US.UTF-8"</span><span class="pln">
sudo dpkg</span><span class="pun">-</span><span class="pln">reconfigure locales</span></pre>
Above 3 commands will reconfigure locales and that should solve the issue.