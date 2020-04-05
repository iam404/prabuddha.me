---
id: 282
title: 'VLC does not support the audio or video format &#8220;hevc&#8221;'
date: 2015-07-08T19:26:09+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=282
permalink: /vlc-not-support-audio-video-format-hevc/
categories:
  - linux
tags:
  - ubuntu
---
VLC out of the box does not play HEVC files. To fix this we need to install <code>libde265</code> via PPA, which is an open source implementation of the h.265 video codec.

Open up the Terminal and run the commands below:
<pre><code>sudo apt-add-repository ppa:strukturag/libde265 
sudo apt-get update 
sudo apt-get install vlc-plugin-libde265
</code></pre>
VLC should now play these media files.

<em>Please note above solution are tested for Ubuntu version 14.04.</em>