---
id: 363
title: Elasticsearch Cheatsheet API
date: 2017-03-24T19:52:38+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=363
permalink: /elasticsearch-cheatsheet-api/
dsq_thread_id:
  - "6210425649"
categories:
  - DevOps
---
<h1 id="elasticsearch-cheatsheet">ElasticSearch Cheatsheet</h1>
&nbsp;

Commonly used Elasticsearch API endpoints and pro-tips I always forgot about in one place!
<h2 id="index-manipulation">Index Manipulation</h2>
<ul>
 	<li>field definition
<ul>
 	<li><strong>type</strong>: string, integer, long, date, float, double, boolean, geo_point</li>
 	<li><strong>index</strong>: non_analyzed, analyzed (default = analyze)</li>
 	<li><strong>store</strong>: true, false (default = false)</li>
 	<li><strong>null_value</strong>: "na"</li>
 	<li><strong>norms</strong></li>
 	<li><strong>similarity</strong>: default: TF/IDF, BM25</li>
 	<li><strong>fielddata</strong></li>
</ul>
</li>
</ul>
<pre><code># delete index
curl -s -XDELETE 'http://localhost:9200/[index_name]/'

# create index with mapping and custom index
curl -s -XPUT 'http://localhost:9200/[index_name]/' -d '{
  "mappings": {
    "document": {
      "properties": {
        "content": {
          "type": "string",
          "analyzer" : "lowercase_with_stopwords"
        }
      }
    }
  },
  "settings" : {
    "index" : {
      "number_of_shards" : 1,
      "number_of_replicas" : 0
    },
    "analysis": {
      "tokenizer" : {
        "pipe":{
           "type" : "pattern",
           "pattern" : "\\|"
        }
      },
      "filter" : {
        "stopwords_filter" : {
          "type" : "stop",
          "stopwords" : ["http", "https", "ftp", "www"]
        }
      },
      "analyzer": {
        "lowercase_with_stopwords": {
          "type": "custom",
          "tokenizer": "lowercase",
          "filter": [ "stopwords_filter" ]
        },
        "csv": {
          "type": "custom",
          "tokenizer": "pipe",
          "filter":["trim", "lowercase"]
        }
      }
    }
  }
}'

# analyze view
http://localhost:9200/[index_name]/_analyze?analyzer=csv&amp;text=Kobe%20Bryant|Lamar%20Odom&amp;pretty

# index document
curl -s -XPUT 'http://localhost:9200/[index_name]/document/1?pretty=true' -d '{
  "content" : "Small content with URL http://example.com."
}'

# retrieve document
curl -s XGET http://localhost:9200/[index_name]/document/1

# refresh index
curl -s -XPOST 'http://localhost:9200/[index_name]/_refresh'

# Human readable list of indices
curl -XGET 'http://localhost:9200/_cat/indices?v'
</code></pre>
&nbsp;
<h2 id="search">Search</h2>
<pre><code># search through url
http://localhost:9200/[index]/[type]/_search?[parameter list]

parameter list:
  * q=title:elasticsearch
  * from=10&amp;size=10 (default size = 10)
  * sort=date:asc (once used no built-in relevancy scoring used)
  * _source=title,date (used if raw document size is too big)

# search with dsl
curl -s -XGET 'http://localhost:9200/url-test/_search?pretty' -d '{
  "query" : {
    "query_string" : {
        "query" : "content:example"
    }
  }
}'
</code></pre>
<h3 id="search-operators-thru-dsl">Search operators through DSL</h3>
<ul>
 	<li><strong>term</strong> (exact match on non-analyzed field or whole document)</li>
 	<li><strong>match</strong> (by defaut search by term under OR opertaor and you can change it via doing "operator": "and")</li>
 	<li><strong>multi_match</strong> (similar to match but it can apply to &gt; 1 fields)</li>
 	<li><strong>query_string</strong>: default search against _all field but you can do [field]:[value] to limit it. You can do perform compound query and negative like " name:nosql AND -description:mongodb"</li>
</ul>
<pre><code># phrase match
curl -s -XGET 'http://localhost:9200/url-test/_search?pretty' -d '{
  "query" : {
    "match" : {
        "name" : {
            "type" : "phrase",
            "query": "enterprise london"
            "slop": 1
         }
    }
  },
  "_source":["name", "desc"]
}'

# multi match against &gt; 1 fields
% curl 'localhost:9200/get-together/_search' -d'{
  "query": {
    "multi_match": {
      "query": "elasticsearch hadoop",
      "fields": [ "name", "description" ]
    }
  } 
}'

# phrase prefix search
% curl 'localhost:9200/get-together/group/_search' –d '
{
  "query": {
    "match": {
        "name": {
             "type": "phrase_prefix",
            "query": "Elasticsearch den",
            "max_expansions": 1
        } 
    }
  },
  "_source": ["name"]
}'

# compound queries
</code></pre>
<h2 id="aggregation">Aggregation</h2>
<p class="comments-section">Aggregations can be categorized as either <strong>Metrics Aggregations</strong> or <strong>Bucket Aggregations</strong>. Metrics Aggregations return a value (single-value e.g. avg) or values (multi-value e.g. stats) calculated over documents returned by the query. Bucket aggregations define criteria to put documents into relevant groups (called buckets).</p>

<pre><code>"aggregations" : {
    "&lt;aggregation_name&gt;" : {
        "&lt;aggregation_type&gt;" : {
            &lt;aggregation_body&gt;
        },
        ["aggregations" : { [&lt;sub_aggregation&gt;]* } ]
    }
    [,"&lt;aggregation_name_2&gt;" : { ... } ]*
}
</code></pre>
<p class="comments-section">Each aggregation can have:</p>

<ul>
 	<li>name</li>
 	<li>type
<ul>
 	<li>value_count</li>
 	<li>cardinality (distinct unqiue)</li>
 	<li>terms (specify a field and show buckets. If the field is analyzed, you are bucketed by its analyzed terms. If the field is non-analyzed, you are bucketed by its whole phrase)</li>
 	<li>avg</li>
 	<li>range</li>
 	<li>geo_distance - returns number of documents within a distance range from a specified origin</li>
</ul>
</li>
 	<li>body</li>
 	<li>sub-aggregation</li>
</ul>
<h2 id="management">Management</h2>
<pre><code># install elasticsearch plugins
./bin/plugin -install mobz/elasticsearch-head
./bin/plugin --install lmenezes/elasticsearch-kopf/1.2
./bin/plugin --install elasticsearch/marvel/latest

# list installed plugins
./bin/plugin --list

# view it
http://localhost:9200/_plugin/head/
http://localhost:9200/_plugin/marvel/sense/index.html

# cluster health
http://localhost:9200/_cluster/health?pretty
http://localhost:9200/_cluster/health?level=indices&amp;pretty
http://localhost:9200/_cluster/health?level=shards&amp;pretty
# see which node is elected as a master
http://localhost:9200/_cluster/state/master_node,nodes?pretty 

# decommissioning a node (elasticsearch will move all shards from the decommissioned node to other nodes in the cluster)
curl -XPUT localhost:9200/_cluster/settings -d '{
    "transient" : {
       "cluster.routing.allocation.exclude._ip" : "192.168.1.10"
    }
}'

# check index mapping
http://localhost:9200/[index_name]/_mapping/[type]?pretty

# list all aliases for an index
http://localhost:9200/[index_name]/_alias/*?pretty

# list nodes with plugins
http://localhost:9200/_nodes?plugin=true&amp;pretty
</code></pre>