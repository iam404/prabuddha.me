---
id: 321
title: Restricting Users To SFTP (Chrooted SFTP)
date: 2016-08-26T22:43:15+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=321
permalink: /restricting-users-sftp-chrooted-sftp/
categories:
  - linux
  - security
---
I will use the user <span class="system">chrooteduser</span> here with the directory <strong><span class="system">/home/chrooteduser</span></strong>. The user <strong><span class="system">chrooteduser</span></strong> belongs to the group <span class="system">users</span>. I want to chroot the user to the <span class="system"><strong>/home/chrooteduser</strong></span> directory.
<h3 id="-enabling-chrooted-sftp">Enabling Chrooted SFTP</h3>
Enabling SFTP is very easy. Open <em><span class="system">/etc/ssh/sshd_config</span></em>...
<pre class="command">$vi /etc/ssh/sshd_config</pre>
... and make sure you have the following line in it
<pre>[...] 

Subsystem sftp internal-sftp 

[...]</pre>
Then add the following stanza at the end of the file (add such a stanza for each user that you want to chroot):
<pre>[...]
Match User <span class="system">chrooteduser</span>
      ChrootDirectory /home/<span class="system">chrooteduser</span>
      AllowTCPForwarding no
      X11Forwarding no
      ForceCommand internal-sftp</pre>
<pre>####
Note:
Instead of adding a stanza for each user, you can also chroot groups, e.g. as follows:

[...]
Match Group users
      ChrootDirectory /home
      AllowTCPForwarding no
      X11Forwarding no
      ForceCommand internal-sftp

#This would chroot all members of the <span class="system">users</span> group to the <span class="system">/home</span> directory.
Please note that all components of the pathname in the <span class="system">ChrootDirectory</span> directive must be root-owned directories that are not writable by any other user or group (see man 5 sshd_config).</pre>
<strong>Restart OpenSSH:</strong>
<pre class="command">/etc/init.d/ssh restart

or

service ssh restart</pre>
&nbsp;

Now, you can log in with an SFTP client, such as <a href="http://filezilla-project.org/" target="_blank" rel="noopener">FileZilla</a>