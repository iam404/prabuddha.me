---
title: Decrypt all Credentials and Private Key values saved in Jenkins vault using Script
date: 2019-05-11T09:58:25+00:00
author: Prabuddha
layout: post
published: true
permalink: /decrypt-all-credentials-and-private-key-values-saved-in-jenkins-vault-using-script/
categories:
  - DevOps
---
<strong>Step 1:</strong>
<h4 id="4b50" class="graf graf--h4 graf-after--p">Access Script Console:</h4>
<p id="d5dc" class="graf graf--p graf-after--h4">Go to “<strong>Manage Jenkins</strong>” then click “<strong>Script Console</strong>”.</p>
&nbsp;

<strong>Step 2:</strong>
<h4 id="a6bf" class="graf graf--h4 graf-after--pre">Decrypt credentials defined in Jenkins.</h4>
Paste and Run below script there.
```java

def creds = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
    com.cloudbees.plugins.credentials.common.StandardUsernameCredentials.class,
    Jenkins.instance,
    null,
    null
);
for (c in creds) {
     println( ( c.properties.privateKeySource ? "ID: " + c.id + ", UserName: " + c.username + ", Private Key: " + c.getPrivateKey() : ""))
}
for (c in creds) {
     println( ( c.properties.password ? "ID: " + c.id + ", UserName: " + c.username + ", Password: " + c.password : ""))
}
```

The above script will decode all the Jenkins credentials and Private keys saved.