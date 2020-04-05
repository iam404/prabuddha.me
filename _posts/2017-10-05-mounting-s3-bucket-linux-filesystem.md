---
id: 535
title: Mounting S3 bucket on Linux Filesystem
date: 2017-10-05T10:13:45+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=535
permalink: /mounting-s3-bucket-linux-filesystem/
dsq_thread_id:
  - "6202798741"
categories:
  - DevOps
  - linux
---
A S3 bucket can be mounted in a AWS instance as a file system using S3fs. S3fs is a FUSE file-system that allows you to mount an Amazon S3 bucket as a local file-system. It behaves like a network attached drive, as it does not store anything on the Instance, but user can access the data on S3 from the instance.

<em><span class="_Tgc">Filesystem in Userspace (<b>FUSE</b>) is a simple interface for userspace programs to export a virtual file-system to the Linux kernel. It also aims to provide a secure method for non privileged users to create and mount their own file-system implementations.</span></em>

<strong>Step-1:- </strong>Install Latest version of S3FS.

On Ubuntu :
<pre>sudo apt-get install automake autotools-dev fuse g++ git libcurl4-gnutls-dev libfuse-dev libssl-dev libxml2-dev make pkg-config
</pre>
On CentOS :
<pre>sudo yum install automake fuse fuse-devel gcc-c++ git libcurl-devel libxml2-devel make openssl-devel
</pre>
Then compile from master via the following commands:
<pre>git clone https://github.com/s3fs-fuse/s3fs-fuse.git
cd s3fs-fuse
./autogen.sh
./configure
make
sudo make install</pre>
<strong>Step-2:-</strong> Use below command to check where s3fs command is placed in O.S. It will also tell you the installation is ok.
<pre>$ <span class="pl-s1">which s3fs</span>
</pre>
<strong>Step-3:-</strong> Getting the access key and secret key.

You will need AWS Access key and Secret key with appropriate permissions in order to access your s3 bucket from your EC2 instance. You can easily manage your user permissions from IAM (Identity and Access Management) Service provided by AWS.

<strong>Step-4 :-</strong> Create a new file in /etc with the name <strong>passwd-s3fs</strong> and Paste the access key and secret key in the below format .
<pre>echo AWSACCESSKEY:AWSSECRETKEY &gt; /etc/passwd-s3fs
chmod 640 /etc/passwd-s3fs
</pre>
<strong>Step-5:-</strong> Now create a directory and mount S3bucket in it. Here, Provide your S3 bucket name in place of “<strong>mybucket</strong>”.
<div id="crayon-59d5e7893b432955667833" class="crayon-syntax crayon-theme-monokai crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" no-popup minimize scroll-mouseover">
<div class="crayon-plain-wrap">

Run s3fs with an existing bucket <code>mybucket</code> and directory <code>/path/to/mountpoint</code>:
<pre><code>s3fs mybucket /path/to/mountpoint -o passwd_file=/etc/passwd-s3fs <span class="crayon-o">-</span><span class="crayon-i">o</span> <span class="crayon-v">allow_other</span>
</code></pre>
</div>
<em>where, <strong>mybucket</strong> is the name of your bucket, <strong>allow_other</strong> is to allow other users to write to the mount-point.</em>

You can unmount it later by simply using the below command.
<pre><code><span class="crayon-v">umount</span> /path/to/mountpoint</code></pre>
<div id="crayon-59d5e7893b434691336115" class="crayon-syntax crayon-theme-monokai crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" no-popup minimize scroll-mouseover">

You can make an entry in /etc/rc.local to automatically remount after reboot.  Find the s3fs binary file by “which” command and make the entry before the “exit 0” line as below.

You can also mount on boot by entering the following line to <code>/etc/fstab</code>:
<pre>s3fs#mybucket /path/to/mountpoint fuse _netdev,allow_other 0 0
</pre>
</div>
</div>
&nbsp;