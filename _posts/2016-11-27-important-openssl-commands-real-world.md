---
id: 347
title: Important OpenSSL Commands in Real World
date: 2016-11-27T01:28:16+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=347
permalink: /important-openssl-commands-real-world/
dsq_thread_id:
  - "6358504141"
categories:
  - DevOps
  - linux
  - security
---
<p class="intro">OpenSSL cheat sheet.</p>

<h3>1. Create new Private Key and Certificate Signing Request</h3>
<pre>openssl req -out <strong>server.csr</strong> -newkey rsa:2048 -nodes -keyout <strong>private.key</strong></pre>
Above command will generate CSR and 2048-bit RSA key file. If you intend to get public certificate then you need to send this CSR file to certificate issuer authority and they will give you signed certificate.
<h3>2. Create Self-Signed Certificate</h3>
<pre>openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout <strong>private.key</strong> -out <strong>cert.pem

# </strong>OR<strong> [optional]
</strong># Sign with an upstream CA (needs CA cert and key)
openssl x509 -req -days 365 -in <strong>client.csr</strong> -CA <strong>ca.crt</strong> -CAkey <strong>ca.key</strong> -set_serial 01 -out <strong>client.crt
</strong></pre>
Above command will generate a self-signed certificate and key file with 2048-bit RSA. You may consider defining –days parameter to extend the validity.
<h3>3. Verify CSR file</h3>
<pre>openssl req -noout -text -in <strong>server.csr</strong></pre>
Verification is important to ensure you are sending CSR to issuer authority with required details.
<h3>4. Create RSA Private Key</h3>
<pre>openssl genrsa -out <strong>private.key</strong> 2048</pre>
If you just need to generate RSA private key, you can use above command. I have included 2048 for stronger encryption.
<h3>5. Remove Passphrase from Key</h3>
<pre>openssl rsa -in <strong>private.key</strong> -out <strong>nopassphrase.key</strong></pre>
If you are using passphrase in key file and using Apache then every time you start, you have to enter the password.
<h3>6. Verify Private Key</h3>
<pre>openssl rsa -in <strong>private.key</strong> -check</pre>
If you doubt on your key file, you can use above command to check.
<h3>7. Verify Certificate File</h3>
<pre>openssl x509 -in <strong>cert.pem</strong> -text -noout</pre>
If you would like to validate certificate data like CN, OU, etc then you can use above command which will give you certificate details.
<h5 class="title"><strong>Verifying that a Certificate is issued by a CA</strong></h5>
<pre>openssl verify -verbose -CAfile <strong>cacert.pem</strong> <strong>server.crt</strong></pre>
<h3>8. Verify the Certificate Signer Authority</h3>
<pre>openssl x509 -in <strong>cert.pem</strong> -noout -issuer -issuer_hash</pre>
Certificate issuer authority signs every certificate and in case you need to check them, you can use above command.
<h3>9. Check Hash Value of A Certificate</h3>
<pre>openssl x509 -noout -hash -in <strong>cert.pem</strong></pre>
<h3>10. Convert DER to PEM format</h3>
<pre>openssl x509 -inform der -in <strong>sslcert.der</strong> -out <strong>sslcert.pem</strong></pre>
Usually, certificate authority will give you SSL cert in .der format and if you need to use them in apache or .pem format, you can use above command to convert them.
<h3>11. Convert PEM to DER format</h3>
<pre>openssl x509 -outform der -in <strong>sslcert.pem</strong> -out <strong>sslcert.der</strong></pre>
In case you need to change .pem format to .der
<h3>12. Convert Certificate and Private Key to PKCS#12 format</h3>
<pre>openssl pkcs12 -export -out <strong>sslcert.pfx</strong> -inkey <strong>key.pem</strong> -in <strong>sslcert.pem</strong></pre>
If you need to use a cert with the java application or with any other who accept only PKCS#12 format, you can use above command, which will generate single pfx containing certificate &amp; key file.

<strong>Tip:</strong> you can also include chain certificate by passing –chain as below.
<pre>openssl pkcs12 -export -out <strong>sslcert.pfx</strong> -inkey <strong>key.pem</strong> -in <strong>sslcert.pem</strong> -chain <strong>cacert.pem</strong></pre>
<h3>13. Create CSR using existing private key</h3>
<pre>openssl req -out <strong>certificate.csr</strong> -key <strong>existing.key</strong> -new</pre>
If you don’t want to create a new private key instead using existing one, you can with above command.
<h3>14. Check contents of PKCS12 format cert</h3>
<pre>openssl pkcs12 -info -nodes -in <strong>cert.p12</strong></pre>
PKCS12 is binary format so you won’t be able to view the content in notepad or another editor. So you got to use above command to view the contents of PKCS12 format file.
<h3>15. Convert PKCS12 format to PEM certificate</h3>
<pre>openssl pkcs12 -in <strong>cert.p12</strong> -out <strong>cert.pem</strong></pre>
If you wish to use existing pkcs12 format with Apache or just in pem format, this will be useful.
<h3>16. Test SSL certificate of particular URL</h3>
<pre>openssl s_client -connect <strong>yoururl.com:443</strong> -showcerts</pre>
<pre># OR<strong> [optional]</strong>
# verify remote cert connectivity with private and CA
openssl s_client -connect <strong>yoururl.com:443</strong> -key <strong>private.key</strong> -CAfile <strong>ca.crt</strong> -state</pre>
I use this quite often to validate the SSL certificate of particular URL from the server. This is very handy to validate the protocol, cipher, and cert details.
<h3>17. Find out OpenSSL version</h3>
<pre>openssl version</pre>
If you are responsible for ensuring OpenSSL is secure then probably one of the first things you got to do is to verify the version.
<ol start="18">
 	<li><strong> Check PEM File Certificate Expiration Date</strong></li>
</ol>
<pre>openssl x509 -noout -in <strong>certificate.pem</strong> -dates</pre>
Useful if you are planning to put some kind of monitoring to check the validity. It will show you date in notBefore and notAfter syntax. notAfter is one you will have to verify to confirm if a certificate is expired or still valid.
<h3>19. Check Certificate Expiration Date of SSL URL</h3>
<pre>openssl s_client -connect <strong>secureurl.com:443</strong> 2&gt;/dev/null | openssl x509 -noout -enddate</pre>
Another useful if you are planning to monitor SSL cert expiration date remotely or particular URL.
<h3>20. Check if SSL V2 or V3 is accepted on URL</h3>
<strong>To check SSL V2</strong>
<pre>openssl s_client -connect <strong>secureurl.com:443</strong> -ssl2</pre>
<strong>To Check SSL V3</strong>
<pre>openssl s_client -connect <strong>secureurl.com:443</strong> -ssl3</pre>
<strong>To Check TLS 1.0</strong>
<pre>openssl s_client -connect <strong>secureurl.com:443</strong> -tls1</pre>
<strong>To Check TLS 1.1</strong>
<pre>openssl s_client -connect <strong>secureurl.com:443</strong> -tls1_1</pre>
<strong>To Check TLS 1.2</strong>
<pre>openssl s_client -connect <strong>secureurl.com:443</strong> -tls1_2</pre>
If you are securing web server and need to validate if SSL V2/V3 is enabled or not, you can use above command. If enabled, you will get “<strong>CONNECTED</strong>” else “<strong>handshake failure</strong>”
<h3>21. Verify if particular cipher is accepted on URL</h3>
<pre>openssl s_client -cipher <strong>'ECDHE-ECDSA-AES256-SHA'</strong> -connect <strong>secureurl:443</strong></pre>
If you are working on security findings and pen test results show some of the weak ciphers is accepted then to validate, you can use above command. Off course, you will have to change the cipher and URL, which you want to test against.

If mentioned cipher is accepted then you will get “<strong>CONNECTED</strong>” else “<strong>handshake failure</strong>”.
<h3>22. <span class="pl-c">Better DH for nginx/Apache</span></h3>
<pre>openssl dhparam -out dhparam.pem 2048</pre>
For stronger DH Param and fix Weak Diffie Hellman Logjam Attack .
<h3>23. <span class="pl-c">Base64 encoding/decoding</span></h3>
<pre>openssl enc -base64 -in myfile -out myfile.b64
openssl enc -d -base64 -in myfile.b64 -out myfile.decoded

echo username:passwd | openssl base64
echo dXNlcm5hbWU6cGFzc3dkCg== | openssl base64 -d</pre>
&nbsp;

&nbsp;