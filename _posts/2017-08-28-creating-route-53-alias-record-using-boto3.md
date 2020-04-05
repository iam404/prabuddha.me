---
id: 511
title: Create Route 53 Alias record Using Boto3
date: 2017-08-28T11:09:34+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=511
permalink: /creating-route-53-alias-record-using-boto3/
dsq_thread_id:
  - "6220771461"
categories:
  - Coding
  - DevOps
---
Simple boto3 helper function to create or update an alias for elb endpoint in Amazon route53.

<span style="color: #ff6600;"><em>Note: As per Amazon Route 53 API Reference, an <code>AliasTarget</code> record should not have a <code>TTL</code> entry.</em></span>
<blockquote>
<pre>import boto3

client = boto3.client(
            'route53',
            aws_access_key_id='XXXXXXXXXXXXXXXXXXXXX',
            aws_secret_access_key='YYYYYYYYYYYYYYYYY'
 )

response = client.change_resource_record_sets(
    HostedZoneId='Z2SXXXXXXXXXXX',
    ChangeBatch={
        'Comment': 'Create/Update ELB dns entry',
        'Changes': [
            {
                'Action': 'UPSERT',
                'ResourceRecordSet': {
                    'Name': 'test.internal.example.com.',
                    'Type': 'A',
                    'AliasTarget': {
                        'HostedZoneId': 'ZXXXXXXXXXXXX',
                        'DNSName': 'internal-a8xxxxxxxxxxxxxxxxxxxxxxxxxxxxx-777777777.us-west-1.elb.amazonaws.com',
                        'EvaluateTargetHealth': False
                    }
                }
            },
        ]
    }
)

print(response)</pre>
</blockquote>
&nbsp;

Reference:-

<a href="http://boto3.readthedocs.io/en/latest/reference/services/route53.html">http://boto3.readthedocs.io/en/latest/reference/services/route53.html</a>