---
id: 963
title: 'Resolve pycurl: libcurl link-time ssl backend (nss) is different from compile-time ssl backend (openssl)'
date: 2019-05-03T10:46:26+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=963
permalink: /resolve-pycurl-libcurl-link-time-ssl-backend-nss-is-different-from-compile-time-ssl-backend-openssl/
categories:
  - DevOps
---
Error:
<pre class="console-output">ImportError: pycurl: libcurl link-time ssl backend (nss) is different from compile-time ssl backend (openssl)
</pre>
&nbsp;
<h2>Fix</h2>
<div class="highlight highlight-source-shell">
<pre>pip uninstall pycurl
<span class="pl-k">export</span> PYCURL_SSL_LIBRARY=nss
pip install --compile --install-option=<span class="pl-s"><span class="pl-pds">"</span>--with-nss<span class="pl-pds">"</span></span> --no-cache-dir pycurl</pre>
</div>
<h2><a id="user-content-more-info" class="anchor" href="https://gist.github.com/ifduyue/ccc644f146657ccb9e60f0af676f1923#more-info" aria-hidden="true"></a>More Info</h2>
<a href="http://pycurl.io/docs/latest/install.html#ssl" rel="nofollow">http://pycurl.io/docs/latest/install.html#ssl</a>