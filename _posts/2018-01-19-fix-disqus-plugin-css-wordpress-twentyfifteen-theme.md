---
id: 628
title: Fix Disqus plugin css in WordPress TwentyFifteen Theme
date: 2018-01-19T13:27:18+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=628
permalink: /fix-disqus-plugin-css-wordpress-twentyfifteen-theme/
dsq_thread_id:
  - "6424205887"
categories:
  - wordpress
tags:
  - css
  - disqus
  - twentyfifteen
  - wordpress
---
The new WordPress Twenty Fifteen theme is pretty nice. But if you’ve just activated this theme and you’re using <a href="https://disqus.com/" target="_blank" rel="noopener">Disqus</a> as a comment system, you might have noticed the comments don’t look right:

To fix Go to <strong>Appearance &gt; Edit CSS</strong>:

Then copy/paste the following CSS into the window:
<pre>
div#disqus_thread {
padding: 3% 10%;
background-color: white;
margin: 3% 0;
box-shadow: 0 0 1px rgba(0,0,0,0.15);
}
@media screen and (min-width: 38.75em) {
div#disqus_thread {
margin: 3% 7.6923%;
}
}
@media screen and (min-width: 59.6875em) {
div#disqus_thread {
margin: 3% 8.3333%;
}
}
 
div#disqus_thread {
 padding: 3% 10%;
 background-color: white;
 margin: 3% 0;
 box-shadow: 0 0 1px rgba(0,0,0,0.15);
}
 
@media screen and (min-width: 38.75em) {
 div#disqus_thread {
 margin: 3% 7.6923%;
 }
}
 
@media screen and (min-width: 59.6875em) {
 div#disqus_thread {
 margin: 3% 8.3333%;
 }
}</pre>
Save it, and take a look at your post again.

&nbsp;

<img class="aligncenter wp-image-629 size-full" src="https://prabuddha.me/wp-content/uploads/2018/01/Screen-Shot-2018-01-19-at-6.53.47-PM.png" alt="" width="2706" height="1130" />

&nbsp;