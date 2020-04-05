---
id: 168
title: How to configure rsnapshot for remote backups ?
date: 2016-05-07T21:56:06+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=168
permalink: /using-rsnapshot-remote-backups/
dsq_thread_id:
  - "6218828279"
categories:
  - DevOps
---
<strong>rsnapshot</strong> is a filesystem snapshot utility based on rsync. rsnapshot makes it easy to make periodic snapshots of local machines, and remote machines over ssh. The code makes extensive use of hard links whenever possible, to greatly reduce the disk space required.

Depending on your configuration, it is quite possible to set up in just a few minutes. Files can be restored by the users who own them, without the root user getting involved.

In this article, let us review how to backup remote Linux host using this utility.
<h3>1. Setup Key Based Authentication</h3>
<pre>#create public &amp; private key on host server
[root@local-host]# ssh-keygen -t rsa

#Copy public key to remote server authorized key file
[root@local-host]# ssh-copy-id -i ~/.ssh/id_rsa.pub remote-host</pre>
<h3>2. Verify the password less login between servers</h3>
Login to the remote-host from local host without entering the password.
<pre>[root@local-host]# ssh remote-host
Last login: Sun Mar 15 16:45:40 2009 from localhost

[root@remote-host]#</pre>
<h3>3. Configure rsnapshot and specify Remote Host Backup Directories</h3>
Define your  rsnapshot root directory where backup would be saved in <strong>/etc/rsnapshot.conf</strong> .
<pre>#vi /etc/rsnapshot.conf

snapshot_root			<span class="highlight"> /.rsnapshot/ </span></pre>
You will also want to uncomment the cmd_ssh line to allow for remote backups.
<pre>cmd_ssh			/usr/bin/ssh</pre>
Remove the “#” from before the cmd_du line to enable accurate disk usage reports.
<pre>cmd_du				/usr/bin/du</pre>
If you had changed the port number on the remote server you are backing up, you may also need to set the below parameter (Replace 25000 with port configured on remote server) .
<pre>ssh_args			-p<span class="highlight"> 25000</span></pre>
Next, we need to consider how many old backups we would like to keep.We set these up under the “BACKUP INTERVALS” section of the configuration.  Edit this section to look like this:
<pre>#########################################
#           BACKUP INTERVALS            #
# Must be unique and in ascending order #
# i.e. hourly, daily, weekly, etc.      #
#########################################

retain		hourly  6
retain		daily   7
retain		weekly  4
retain		monthly 3</pre>
Now define your remote-host destination backup directories in <strong>/etc/rsnapshot.conf</strong> as shown below. In this example,
<ul>
 	<li><strong>root@remote-host:/var/www/</strong> – Source directory on the remote-host that should be backed-up. i.e remote backup destination directory.</li>
 	<li><strong>remote-host-backup/</strong> – destination directory where the backup of the remote-host will be stored.</li>
</ul>
<pre>backup  root@remote-host:/var/www/  remote-host-backup/</pre>
<h3>4. Test rsnapshot Configuration</h3>
Perform configuration test to make sure rsnapshot is setup properly.
<pre># rsnapshot configtest
Syntax OK</pre>
<h3>5. Add Crontab Entry for rsnapshot</h3>
Once you’ve verified that the rsync hourly and daily backup configurations are setup properly in the rsnapshot rsync utility, it is time to set this in the crontab as shown below.
<pre># crontab -e
0 0/4 * * * /usr/bin/rsnapshot hourly
@daily /usr/bin/rsnapshot daily
@weekly /usr/bin/rsnapshot weekly
30 2 1 * * /usr/bin/rsnapshot monthly</pre>
<h3>6. Manually test the remote-host backup once</h3>
<pre>[root@local-host]# /usr/bin/rsnapshot hourly

[root@local-host]# ls -l /.rsnapshots/hourly.0/
total 8
drwxr-xr-x 3 root root 4096 Jul 22 04:19 remote-host-backup
drwxr-xr-x 3 root root 4096 Jul 13 05:07 localhost

[root@local-host]# ls -l /.rsnapshots/hourly.0/remote-host-backup/
total 4
drwxr-xr-x 93 root root 4096 Jul 22 03:36 var</pre>
&nbsp;