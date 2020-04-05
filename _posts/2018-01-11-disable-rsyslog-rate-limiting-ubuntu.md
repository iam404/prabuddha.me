---
id: 590
title: How to disable rsyslog Rate-Limiting in ubuntu
date: 2018-01-11T06:11:38+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=590
permalink: /disable-rsyslog-rate-limiting-ubuntu/
dsq_thread_id:
  - "6405922794"
categories:
  - Uncategorized
---
<section class="field_kcs_issue_txt">
<h2>Issue</h2>
<ul>
 	<li>The following messages have been reported in <code>/var/log/syslog</code>:</li>
</ul>
<div class="code-raw">
<pre>Jul  3 08:50:16 localhost rsyslogd-2177: imuxsock lost 838 messages from pid 4713 due to rate-limiting
Jul  3 08:50:17 localhost rsyslogd-2177: imuxsock begins to drop messages from pid 4713 due to rate-limiting
</pre>
</div>
<ul>
 	<li>How to disable rsyslog <code>rate-limiting</code> in order to prevent these messages?</li>
</ul>
<h2>Fix</h2>
To disable it, add
<div class="highlighter-rouge">
<pre class="highlight">$SystemLogRateLimitInterval 0
$SystemLogRateLimitBurst 0
</pre>
</div>
to your <code class="highlighter-rouge">/etc/rsyslog.conf</code>.

</section>
<blockquote><em>Note: Above fix shall work in RHEL instances also.</em></blockquote>