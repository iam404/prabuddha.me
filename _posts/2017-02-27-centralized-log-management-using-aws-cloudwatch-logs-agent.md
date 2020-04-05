---
id: 405
title: Centralized Log Management using AWS CloudWatch Logs Agent
date: 2017-02-27T03:17:46+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=405
permalink: /centralized-log-management-using-aws-cloudwatch-logs-agent/
dsq_thread_id:
  - "6413417597"
categories:
  - aws
  - DevOps
---
True to AWS form, getting started is incredibly <a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/QuickStartEC2Instance.html" target="_blank" rel="noopener">simple</a>. Using the defaults for all of the installer questions, I was able to dump all of my log data from "/var/log/messages" to CloudWatch:

<strong>1. Create an EC2 role with the following perms:</strong>
<div class="highlight">
<pre><span class="p">{</span>
  <span class="nt">"Version"</span><span class="p">:</span> <span class="s2">"2012-10-17"</span><span class="p">,</span>
  <span class="nt">"Statement"</span><span class="p">:</span> <span class="p">[</span>
    <span class="p">{</span>
      <span class="nt">"Effect"</span><span class="p">:</span> <span class="s2">"Allow"</span><span class="p">,</span>
      <span class="nt">"Action"</span><span class="p">:</span> <span class="p">[</span>
        <span class="s2">"logs:*"</span><span class="p">,</span>
        <span class="s2">"s3:GetObject"</span>
      <span class="p">],</span>
      <span class="nt">"Resource"</span><span class="p">:</span> <span class="p">[</span>
        <span class="s2">"arn:aws:logs:us-east-1:*:*"</span><span class="p">,</span>
        <span class="s2">"arn:aws:s3:::*"</span>
      <span class="p">]</span>
    <span class="p">}</span>
  <span class="p">]</span>
<span class="p">}</span>
</pre>
</div>
<strong>2. Launch a Linux instance with the above role assigned.</strong>
<strong>3. Log on to the new instance and then download and install the client:</strong>
<div class="highlight">
<pre><span class="vg">wget</span> <span class="nl">https:</span><span class="o">//</span><span class="vg">s3</span><span class="o">.</span><span class="vg">amazonaws</span><span class="o">.</span><span class="vg">com</span><span class="o">/</span><span class="vg">aws</span><span class="o">-</span><span class="vg">cloudwatch</span><span class="o">/</span><span class="vg">downloads</span><span class="o">/</span><span class="vg">awslogs</span><span class="o">-</span><span class="vg">agent</span><span class="o">-</span><span class="vg">setup</span><span class="o">-</span><span class="vg">v1</span><span class="mf">.0</span><span class="o">.</span><span class="vg">py</span>
<span class="vg">sudo</span> <span class="vg">python</span> <span class="o">./</span><span class="vg">awslogs</span><span class="o">-</span><span class="vg">agent</span><span class="o">-</span><span class="vg">setup</span><span class="o">-</span><span class="vg">v1</span><span class="mf">.0</span><span class="o">.</span><span class="vg">py</span> <span class="o">--</span><span class="vg">region</span> <span class="vg">us</span><span class="o">-</span><span class="vg">east</span><span class="il">-1</span>
</pre>
</div>
<strong>4. Select all the defaults.</strong>
<strong>5. Go to Cloudwatch in the AWS Console and select "Logs" in the left pane.</strong>
<strong>6. You should now see "/var/log/messages" in the "Log Groups" list.</strong>
<strong>7. Select this new log group and click "Create Metric Filter"</strong>
<strong>8. For our example, just put "Warning:" in the Filter Pattern box (With quotes).</strong>
<strong>9. Click "Assign Metric"</strong>
<strong>10. Give your metric a name (I chose "Warning").</strong>
<strong>11. Click "Create Filter"</strong>

That's it! You can now create alarms based on filters like this one. In its most basic form, this is very simple and I'm very excited about this new feature.