---
id: 487
title: Securely Store Passwords and Api Keys Using Vault
date: 2017-08-26T22:06:54+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=487
permalink: /securely-store-passwords-api-keys-using-vault/
dsq_thread_id:
  - "6263541210"
categories:
  - DevOps
  - security
---
Vault is a tool that is used to access secret information securely, it may be password, API key, certificate or anything else. Vault provides a unified interface to secret information through strong access control mechanism and extensive logging of events.

To bootstrap Vault, you need to download the binary from <a href="https://www.vaultproject.io/downloads.html">https://www.vaultproject.io/downloads.html</a>. Vault is written in Go and binaries are available for various platforms.
Unzip the downloaded file and you are ready to use Vault.

Start Vault Server next. You need a configuration file to specify some options.

<strong>vault.conf</strong>
<pre>backend "inmem" { 
}

listener "tcp" {
  address = "0.0.0.0:8200"
  tls_disable = 1 
}

disable_mlock = true</pre>
<span style="color: #ff0000;">Don’t use this config in production.</span>

Start Vault with
<pre class="prettyprint"><span class="pln">$ vault server </span><span class="pun">-</span><span class="pln">config vault</span><span class="pun">.</span><span class="pln">conf</span></pre>
Vault will start as a foreground process.

Congratulations, you started Vault.

Now is a good moment to open a second console to perform administrative tasks with Vault. Vault runs now in plaintext mode because TLS/SSL is disabled. You need to set the <code>VAULT_ADDR</code> environment variable to tell the Vault client to use plaintext:
<pre class="prettyprint"><span class="pln">$ </span><span class="kwd">export</span><span class="pln"> VAULT_ADDR</span><span class="pun">=</span><span class="pln">http</span><span class="pun">:</span><span class="com">//127.0.0.1:8200</span></pre>
Vault is started. It requires two additional steps before you can actually start using Vault. Vault needs to be initialized and unsealed. Initialization is the process of initial key generation. Unsealing is supplying the keys to Vault so Vault can decrypt encrypted data and start serving clients.

Vault creates upon initialization two things:
<ol>
 	<li>The master key and key splits</li>
 	<li>A root token</li>
</ol>
<strong>Initialize Vault with:</strong>
<pre class="prettyprint"><code class="prettyprint bash"><span class="pln">$ vault init </span><span class="pun">-</span><span class="pln">key</span><span class="pun">-</span><span class="pln">shares</span><span class="pun">=</span><span class="lit">5</span> <span class="pun">-</span><span class="pln">key</span><span class="pun">-</span><span class="pln">threshold</span><span class="pun">=</span><span class="lit">2</span></code></pre>
Vault will display the key shares and the root key. Please note that these values are random and change upon every initialization. <span style="color: #ff0000;">Be careful with that output as you will see it only once. There’s no way to retrieve the keys and the token afterward. Please read the instructions carefully when using Vault with real data otherwise you’ll loose your data.</span>
<pre>Key 1: 99eb89735688ad7a29bb1ff27383bd1005a22a62c97f14357ea4f5f98c1d2c8c01
Key 2: 0c5605b16905794a302603bbeb8f6c8ad5ecf7e877f0e29084f838eba931b86902
Key 3: 7f3d88067c7e355acea4fe756a8b23fc6cd6bc671d7cb0f3d2cc8ae543dc3dc303
Key 4: 3d37062e1704ca2a02073b29c097d5a56e7056e710f515c16b40b9cfe3698bb804
Key 5: 4e5c8b99027f863afc85c6e741939ad3d74a1d687a7947a23d740bc109840e1205
Initial Root Token: 9a63de21-8af7-311a-9a5a-151b6a0d4795

Vault initialized with 5 keys and a key threshold of 2. Please
securely distribute the above keys. When the Vault is re-sealed,
restarted, or stopped, you must provide at least 2 of these keys
to unseal it again.

Vault does not store the master key. Without at least 2 keys,
your Vault will remain permanently sealed.</pre>
Then you need to unseal Vault. Vault does not store the key on disk. It’s stored in memory all the time. After initializing and after (re)starting Vault you’re required to unseal Vault with the required number of key shares so Vault can serve secrets. In this case that’s two key shares. Note: There’s also a seal command to make Vault stop serving secrets.
<pre class="prettyprint"><code class="prettyprint"><span class="pln">$ vault unseal </span><span class="lit">99eb89735688ad7a29bb1ff27383bd1005a22a62c97f14357ea4f5f98c1d2c8c01</span>
<span class="typ">Sealed</span><span class="pun">:</span> <span class="kwd">true</span>
<span class="typ">Key</span> <span class="typ">Shares</span><span class="pun">:</span> <span class="lit">5</span>
<span class="typ">Key</span> <span class="typ">Threshold</span><span class="pun">:</span> <span class="lit">2</span>
<span class="typ">Unseal</span> <span class="typ">Progress</span><span class="pun">:</span> <span class="lit">1</span><span class="pln">

$ vault unseal </span><span class="lit">7f3d88067c7e355acea4fe756a8b23fc6cd6bc671d7cb0f3d2cc8ae543dc3dc303</span>
<span class="typ">Sealed</span><span class="pun">:</span> <span class="kwd">false</span>
<span class="typ">Key</span> <span class="typ">Shares</span><span class="pun">:</span> <span class="lit">5</span>
<span class="typ">Key</span> <span class="typ">Threshold</span><span class="pun">:</span> <span class="lit">2</span>
<span class="typ">Unseal</span> <span class="typ">Progress</span><span class="pun">:</span> <span class="lit">0</span></code></pre>
Once Vault is unsealed you can start storing secret data inside of Vault.

Vault requires an authenticated access to proceed from here on. Vault uses tokens as generic authentication on its transport level.

Remember the output from the initialization? The last item after the key shares is the root token. The easiest way for now is using the root token. The easiest way to use the token on the console is storing it in an environment variable:
<pre class="prettyprint"><code class="prettyprint"><span class="pln">$ </span><span class="kwd">export</span><span class="pln"> VAULT_TOKEN</span><span class="pun">=</span><span class="lit">9a63de21</span><span class="pun">-</span><span class="lit">8af7</span><span class="pun">-</span><span class="lit">311a</span><span class="pun">-</span><span class="lit">9a5a</span><span class="pun">-</span><span class="lit">151b6a0d4795</span>
</code></pre>
The generic secret backend allows storage of arbitrary values as a key-value store. A single context can store one or many key-value tuples. Contexts can be organized hierarchically and the used data format is JSON.
<pre>vault write secret/my-application value=7175b4cfff396de2c136d052bfac6b0d4
Success! Data written to: secret/my-application

vault read secret/my-application
Key            Value
lease_duration 2592000
value          7175b4cfff396de2c136d052bfac6b0d4

# Just the value
vault read -field=value secret/my-application
7175b4cfff396de2c136d052bfac6b0d4</pre>
To retrieve the <code>my-application</code> secret key from your apps, you have to do something like this:
<pre># Using Python for this example, should be similar in other languages.
import requests
headers = {"X-Vault-Token": "9a63de21-8af7-311a-9a5a-151b6a0d4795"}

r = requests.get('http://127.0.0.1:8200/v1/secret/my-application', headers=headers)
json_response = r.json()
# {u'lease_id': u'', u'warnings': None, u'auth': None, u'lease_duration': 2592000,
# u'data': {u'value': u'7175b4cfff396de2c136d052bfac6b0d4'}, u'renewable': False}

if r.status_code == 200:
   api_key = json_response.get('data',{}).get('value', "")
   # 7175b4cfff396de2c136d052bfac6b0d4</pre>
<h3>Deploying Vault with AWS S3 storage</h3>
<pre># vault.hcl
backend "s3" {
 bucket = "vault"
 access_key = "AKIAIPR2HA"
 secret_key = "nO6TK2NAPpZkBf+iSWrnQAojIp"
 region = "eu-west-1"
}

listener "tcp" {
 address = "127.0.0.1:8200"
 tls_disable = 1
}</pre>
<pre>$vault server -config=vault.hcl

$vault init

# Key 1: 4cdc4cccb9e2ddda8a2163e8d1a1b94522e6e9c6d25d6a5a12e9677262547c1401
# Key 2: 8b94a7e4614956778ccce9a185d736e9d4a86dd75ca588056f9c10da60a8f31d02
# Key 3: 42378fbd9549091da0bcf6c14f9287e961e0738eb129b4b28d79538e702ab9db03
# Key 4: b35c835644358269219fd75378507380d9cc76ca47fe5f64ce28b28e9eb5f82c04
# Key 5: 7affab0fb035dd030defc833b215c2806c846893aa7263d32ccdf1da8e37b2ea05
# Initial Root Token: 8a5dbab4-43cc-8b8d-e6a6-04d5e458dca9

# You have to provide any 3 of the 5 keys printed to stdout after the init command
$vault unseal

# authenticate with the root token, configure Vault, set policies, etc.
$vault auth 8a5dbab4-43cc-8b8d-e6a6-04d5e458dca9</pre>
&nbsp;