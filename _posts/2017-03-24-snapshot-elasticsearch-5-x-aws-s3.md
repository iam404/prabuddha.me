---
id: 370
title: Snapshot Elasticsearch in AWS S3
date: 2017-03-24T20:25:44+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=370
permalink: /snapshot-elasticsearch-5-x-aws-s3/
dsq_thread_id:
  - "6249428383"
categories:
  - DevOps
---
<h2>Snapshot and Restore Elasticsearch 5.x in S3.</h2>
<h3>Register S3 repository</h3>
<pre>curl -XPUT 'http://localhost:9200/_snapshot/s3_backup' -d '{
    "type": "s3",
    "settings": {
        "access_key": "ACCESS_KEY_TOCKEN",
        "secret_key": "SECURITY_KEY_TOCKEN",
        "bucket": "es-backups",  
        "region": "us-west-1",
        "base_path": "elasticsearch",
        "max_retries": 3
    }
}'</pre>
<pre>Response-&gt;{"acknowledged":true}</pre>
<h3>Create a Snapshot</h3>
<pre>#For all indices
curl -XPUT http://localhost:9200/_snapshot/s3_backup/backup_1/\?wait_for_completion=true</pre>
<pre># Or for specific indices
curl -XPUT 'http://localhost:9200/_snapshot/s3_backup/backup_1?wait_for_completion=true' -d '{
    "indices": "your_index1, your_index2",
    "ignore_unavailable": "true",
    "include_global_state": false
}'</pre>
<pre>Set "wait_for_completion=false" to run snapshot in background.</pre>
<h3>Restoring</h3>
<pre>curl -XPOST 'http://localhost:9200/_snapshot/s_backup/backup_1/_restore' -d '{
    "indices": "your index",
    "ignore_unavailable": "true",
    "include_global_state": false
}'</pre>
<h3>Some commands to view information</h3>
<pre>#List all registries
curl -X GET http://localhost:9200/_snapshot/_all?pretty=true</pre>
<pre>#List all snapshot
curl -XGET "http://localhost:9200/_snapshot/s3_backup/_all"</pre>
<pre>#View status
curl -XGET "http://localhost:9200/_snapshot/s3_backup/backup_1/_status"</pre>
<pre>#Detail information of snapshot
curl -XGET "http://localhost:9200/_snapshot/s3_backup/backup_1"</pre>
<pre>#Delete a snapshot
curl -XDELETE "http://localhost:9200/_snapshot/s3_backup/backup_1"</pre>
<pre>#Delete all snapshot
curl -XDELETE "http://localhost:9200/_snapshot/s3_backup"</pre>