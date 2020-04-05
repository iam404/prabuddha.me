---
id: 424
title: Apache Bench (ab) to POST JSON to an API
date: 2017-05-10T17:46:12+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=424
permalink: /apache-bench-ab-post-json-api/
dsq_thread_id:
  - "6203705895"
categories:
  - Uncategorized
---
<strong>Example of using Apache Bench (ab) to POST JSON to an API</strong>
<ol>
 	<li>Create a JSON file<strong> test.json</strong></li>
</ol>
```

$ cat test.json
json='{ "timestamp" : 1484825894873, "test" : "test"}'

```

2. Run the test
```

ab -c 10 -n 1000 -p  test.json -T application/x-www-form-urlencoded  https://example.com/test

```