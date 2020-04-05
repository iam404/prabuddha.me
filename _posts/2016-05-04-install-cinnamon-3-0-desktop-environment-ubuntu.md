---
id: 138
title: Install Cinnamon 3.0 on Ubuntu 16.04 LTS
date: 2016-05-04T19:51:59+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=138
permalink: /install-cinnamon-3-0-desktop-environment-ubuntu/
image: /wp-content/uploads/2016/05/cinnamon-30.png
categories:
  - linux
---
To install the Cinnamon 3.0 desktop environment in the Ubuntu 16.04 LTS or Ubuntu 15.10/14.04 you’ll need to add the following PPA (Personal Package Archive).
<pre class="wp-code-highlight prettyprint prettyprinted"><span class="pln">sudo apt</span><span class="pun">-</span><span class="pln">add</span><span class="pun">-</span><span class="pln">repository ppa</span><span class="pun">:</span><span class="pln">embrosyn</span><span class="pun">/</span><span class="pln">cinnamon
sudo apt</span><span class="pun">-</span><span class="kwd">get</span><span class="pln"> update
sudo apt</span><span class="pun">-</span><span class="kwd">get</span><span class="pln"> dist</span><span class="pun">-</span><span class="pln">upgrade
sudo apt</span><span class="pun">-</span><span class="kwd">get</span><span class="pln"> install cinnamon</span></pre>
Once you've executed all the above-mentioned commands in the Terminal application and everything is successfully installed, you can enter the Cinnamon session.

You can do that by logging out and choosing "Cinnamon" from the log in screen, by clicking on the small Ubuntu logo located on the right side of the user name.

<img class="aligncenter wp-image-142 size-full" src="https://prabuddha.me/wp-content/uploads/2016/05/cinamon.gif" alt="cinamon" width="708" height="364" />

To Remove Cinnamon 3.0 installation from your Ubuntu OS:
<pre class="wp-code-highlight prettyprint prettyprinted"><span class="pln">sudo apt</span><span class="pun">-</span><span class="kwd">get</span><span class="pln"> install ppa</span><span class="pun">-</span><span class="pln">purge
sudo ppa</span><span class="pun">-</span><span class="pln">purge ppa</span><span class="pun">:</span><span class="pln">embrosyn</span><span class="pun">/</span><span class="pln">cinnamon</span></pre>