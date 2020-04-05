---
id: 654
title: Error installing Ruby 2.2.3 in Ubuntu
date: 2017-03-12T12:05:49+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=654
permalink: /error-installing-ruby-2-2-3-ubuntu/
dsq_thread_id:
  - "6541460263"
categories:
  - DevOps
---
<h1><strong>Error:</strong></h1>
<pre>compiling unixsocket.c
ossl_ssl.c:141:27: error: 'SSLv3_method' undeclared here (not in a function)
 OSSL_SSL_METHOD_ENTRY(SSLv3),
 ^
ossl_ssl.c:119:69: note: in definition of macro 'OSSL_SSL_METHOD_ENTRY'
 #define OSSL_SSL_METHOD_ENTRY(name) { #name, (SSL_METHOD *(*)(void))name##_method }
 ^
ossl_ssl.c:142:27: error: 'SSLv3_server_method' undeclared here (not in a function)
 OSSL_SSL_METHOD_ENTRY(SSLv3_server),
 ^
ossl_ssl.c:119:69: note: in definition of macro 'OSSL_SSL_METHOD_ENTRY'
 #define OSSL_SSL_METHOD_ENTRY(name) { #name, (SSL_METHOD *(*)(void))name##_method }
 ^
ossl_ssl.c:143:27: error: 'SSLv3_client_method' undeclared here (not in a function)
 OSSL_SSL_METHOD_ENTRY(SSLv3_client),
 ^
ossl_ssl.c:119:69: note: in definition of macro 'OSSL_SSL_METHOD_ENTRY'
 #define OSSL_SSL_METHOD_ENTRY(name) { #name, (SSL_METHOD *(*)(void))name##_method }
 ^
Makefile:293: recipe for target 'ossl_ssl.o' failed
make[2]: *** [ossl_ssl.o] Error 1
make[2]: Leaving directory '/tmp/ruby-build.20180312113832.25715/ruby-2.2.3/ext/openssl'
exts.mk:201: recipe for target 'ext/openssl/all' failed
make[1]: *** [ext/openssl/all] Error 2
make[1]: *** Waiting for unfinished jobs....
compiling unixserver.c
compiling option.c</pre>
<h1>Fix:</h1>
<pre>curl -fsSL https://gist.github.com/mislav/055441129184a1512bb5.txt | rbenv install --patch 2.2.3</pre>
&nbsp;