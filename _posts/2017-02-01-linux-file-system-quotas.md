---
id: 749
title: Linux File System Quotas
date: 2017-02-01T00:18:48+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=749
permalink: /linux-file-system-quotas/
categories:
  - linux
---
<b>Disk Quotas:</b> This feature of Linux allows the system administrator to allocate a maximum amount of disk space a user or group may use. It can be flexible in its adherence to the rules assigned and is applied per filesystem. The default Linux Kernel which comes with Redhat and Fedora Core comes with quota support compiled in.

Two versions of quotas have been released. Version 2 is used by the Linux 2.4 and 2.6 kernel. Quotas version 1 is used by the Linux 2.2 kernel. Both are discussed in this tutorial.
<div class="labelbar">Configuration:</div>
Configuration of disk usage quotas on Linux - Perform the following as root:
<ol>
 	<li>Edit file <tt>/etc/fstab</tt> to add qualifier "usrquota" or "grpquota" to the partition. The following file system mounting options can be specified in <tt>/etc/fstab</tt>: grpquota, noquota, quota and usrquota. (These options are also accepted by the <tt>mount</tt> command but ignored.) The filesystem when mounted will show up in the file <tt>/etc/mtab</tt>, the list of all currently mounted filesystems.)
<ul>
 	<li>To enable user quota support on a file system, add "usrquota" to the fourth field containing the word "defaults".
<pre class="code-box">...
/dev/hda2     /home   ext3    defaults,usrquota              1    1
...
</pre>
</li>
 	<li>Replace "usrquota" with "grpquota", should you need group quota support on a file system.
<pre class="code-box">...
/dev/hda2     /home   ext3    defaults,grpquota              1    1
...
</pre>
</li>
 	<li>Need both user quota and group quota support on a file system?
<pre class="code-box">...
/dev/hda2     /home   ext3    defaults,usrquota,grpquota     1    1
...
</pre>
This enables user and group quotas support on the <tt>/home</tt> file system.</li>
</ul>
</li>
 	<li><tt>touch /<i>partition</i>/aquota.user</tt>
where the partition might be <tt>/home</tt> or some partition defined in <tt>/etc/fstab</tt>.
then
<tt>chmod 600 /<i>partition</i>/aquota.user</tt>The file should be owned by root. Quotas may also be set for groups by using the file <tt>aquota.group</tt>

Quota file names:
<ul>
 	<li>Quota Version 2 (Linux 2.4/2.6 kernel: Red Hat 7.1+/8/9,FC 1-3): <tt>aquota.user, aquota.group</tt></li>
 	<li>Quota Version 1 (Linux 2.2 kernel: Red Hat 6, 7.0): <tt>quota.user, quota.group</tt></li>
</ul>
The files can be converted/upgraded using the <tt><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=convertquota">convertquota</a></tt> command.</li>
 	<li>Re-boot or re-mount file partition with quotas.
<ul>
 	<li>Re-boot: <tt>shutdown -r now</tt></li>
 	<li>Re-mount partition: <tt>mount -o remount <i>/partition</i></tt></li>
</ul>
After re-booting or re-mounting the file system, the partition will show up in the list of mounted filesystems as having quotas. Check <tt>/etc/mtab</tt>:
<pre class="code-box">...
/dev/hda5 / ext3 rw,usrquota 0 0
...
</pre>
</li>
 	<li><tt>quotacheck -vgum /<i>partition</i></tt>
or
<tt>quotacheck -vguma</tt>
<ul>
 	<li>For example (Linux kernel 2.4+: Red Hat 7.1+, Fedora): <tt>quotacheck -vguma</tt>
<pre class="code">quotacheck: WARNING -  Quotafile //aquota.user was probably truncated. ...
quotacheck: Scanning /dev/hda5 [/] done
quotacheck: Checked 9998 directories and 179487 files
</pre>
</li>
 	<li>For example (Linux kernel 2.2: Red Hat 6/7.0): <tt>quotacheck -v /dev/hda6 </tt>
System response:
<pre class="code">      Scanning /dev/hda6 [/home] done
      Checked 444 directories and 3136 files  
      Using quotafile /home/quota.user       
</pre>
</li>
</ul>
Quotacheck is used to scan a file system for disk usages, and updates the quota record file "quota.user/aquota.user" to the most recent state. It is recommended thet quotacheck be run at bootup (part of Redhat default installation)

Man page: <a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=quotacheck">quotacheck</a> - scan a filesystem for disk usage, create, check and repair quota files</li>
 	<li><tt>quotaon -av</tt>
System Response: <tt>/dev/hda6: user quotas turned on</tt>quotaon - enable disk quotas on a file system.
quotaoff - turn off disk quotas for a file system.

Man page: <a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=quotaon">quotaon</a> - turn filesystem quotas on and off</li>
 	<li><tt>edquota -u <i>user_id</i></tt>
Edit directly using vi editor commands. (See below for more info.)
For example: <tt>edquota -u user1</tt>
<ul>
 	<li>System Response (RH 7+):
<pre class="code">Disk quotas for user <i>user1</i> (uid 501):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/hda5                      1944          0          0        120        0        0
</pre>
<ul>
 	<li>blocks: 1k blocks</li>
 	<li>inodes: Number of entries in directory file</li>
 	<li>soft: Max number of blocks/inodes user may have on partition before warning is issued and grace persiod countdown begins.
If set to "0" (zero) then no limit is enforced.</li>
 	<li>hard: Max number of blocks/inodes user may have on partition.
If set to "0" (zero) then no limit is enforced.</li>
</ul>
</li>
 	<li>System Response (RH 6):
<pre class="code">           Quotas for user user1:
           /dev/sdb6: blocks in use: 56, limits (soft = 0, hard = 0)
                   inodes in use: 50, limits (soft = 0, hard = 0)
</pre>
Something failed if you get the response:
<pre class="code">           /dev/sdb6: blocks in use: 0, limits (soft = 0, hard = 0)
                   inodes in use: 0, limits (soft = 0, hard = 0)

</pre>
Edit limits:
<pre class="code">           Quotas for user user1: 
           /dev/hda6: blocks in use: 992, limits (soft = 50000, hard = 55000) 
                   inodes in use: 71, limits (soft = 10000, hard = 11000)
</pre>
</li>
</ul>
If editing group quotas: <tt>edquota -g <i>group_name</i></tt>

Man page: <a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=edquota">edquota</a> - edit user quotas</li>
 	<li>List quotas:
<tt>quota -u <i>user_id</i></tt>For example: <tt>quota -u user1</tt>
System response:
<pre class="code">Disk quotas for user user1 (uid 501):
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
      /dev/hda6     992   50000   55000              71   10000   11000
</pre>
If this does not respond similar to the above, then restart the computer: <tt>shutdown -r now</tt>Man page: <a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=quota">quota</a> - display disk usage and limits</li>
</ol>
<div class="labelbar">Quota Reports:</div>
<ul>
 	<li>Report on all users over quota limits: <tt>quota -q</tt></li>
 	<li>Quota summary report: <tt>repquota -a</tt>
<pre class="code">*** Report for user quotas on device /dev/hda5
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      -- 4335200       0       0         181502     0     0
bin       --   15644       0       0            101     0     0
...
<i>user1</i>     --    1944       0       0            120     0     0
</pre>
No limits shown with this user as limits are set to 0.Man page: <a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=repquota">repquota</a> - summarize quotas for a filesystem.</li>
</ul>
<div class="labelbar">Cron:</div>
Quotacheck should scan the file system via cronjob periodically (say, every week?). Add a script to the /etc/cron.weekly/ directory.
File: /etc/cron.weekly/runQuotacheck
<ul>
 	<li>Linux Kernel 2.4: Red Hat 7.1 - Fedora Core 3:
<pre class="code-box">#!/bin/bash
/sbin/quotacheck -vguma</pre>
</li>
 	<li>Linux Kernel 2.2: Red Hat 6/7.0:
<pre class="code-box">#!/bin/bash
/sbin/quotacheck -v -a</pre>
</li>
</ul>
(Remember to <tt>chmod +x /etc/cron.weekly/runQuotacheck</tt>)
<div class="labelbar">Edquota Note:</div>
The "edquota" command puts you into a "vi" editing mode so knowledge of the "vi" editor is necessary. Another editor may be specified with the <b>EDITOR</b>environment variable. You are <b>NOT</b> editing the quota.user file directly. The /<i>partition</i>/quota.user or quota.group file is a binary file which you do not edit directly. The command edquota gives you an ascii interface with the text prepared for you. When you ":wq" to save the file from the vi session, it is converted to binary by the edquota command and stored in the quota.user file.

Assigning quota for a bunch of users with the same value. To rapidly set quotas for all users, on my system to the same value as user user1, I would first edit user <tt>user1</tt>'s quota information by hand, then execute:
<pre class="code">  edquota -p user1 `awk -F: '$3 &gt; 499 {print $1}' /etc/passwd`</pre>
This assumes that the user uid's start from 500 and increment upwards. "blocks in use" is the total number of blocks (in kilobytes) a user has comsumed on a partition. "inodes in use" is the total number of files a user has on a partition.

edquota options:
<table class="box">
<thead>
<tr>
<th>Option</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>-r
-m</td>
<td valign="top">Edit quotas on remote server using RPC. Remote server must be configured with the daemon <tt>rpc.rquotad</tt></td>
</tr>
<tr>
<td>-u</td>
<td>Edit user quota</td>
</tr>
<tr>
<td>-g</td>
<td>Edit group quota</td>
</tr>
<tr>
<td>-p <i>user-id</i></td>
<td>Duplicate the quotas based on existing prototype user</td>
</tr>
<tr>
<td>-F <i>format</i>
-F vfsold
-F vfsv0
-F rpc
-F xfs</td>
<td>Format:
vfsold - version 1
vfsv0 - version 2
rpc - quotas over NFS
xfs - quotas for XFS filesystem</td>
</tr>
<tr>
<td>-f <i>/file-system</i></td>
<td>Perform on specified filesystem. Default is to apply on all filesystems with quotas</td>
</tr>
<tr>
<td>-t</td>
<td>Edit the soft time limits for each filesystem.</td>
</tr>
<tr>
<td>-T</td>
<td>Edit time for user/group when softlimit is enforced. Specify number and unit or "unset"</td>
</tr>
</tbody>
</table>
<b>Soft Limit and Hard Limits:</b>

Soft limit indicates the maximum amount of disk usage a quota user has on a partition. When combined with "grace period", it acts as the border line, which a quota user is issued warnings about his impending quota violation when passed. Hard limit works only when "grace period" is set. It specifies the absolute limit on the disk usage, which a quota user can't go beyond his "hard limit".

<b>Grace Period:</b>

"Grace Period" is configured with the command "<tt>edquota -t</tt>", "grace period" is a time limit before the "soft limit" is enforced for a file system with quota enabled. Time units of sec(onds), min(utes), hour(s), day(s), week(s), and month(s) can be used. This is what you'll see with the command "<tt>edquota -t</tt>":

System response:
<ul>
 	<li>Linux Kernel 2.4+: Red Hat 7.1+/Fedora:
<pre class="code">Grace period before enforcing soft limits for users:
Time units may be: days, hours, minutes, or seconds
  Filesystem             Block grace period     Inode grace period
  /dev/hda5                     7days                  7days</pre>
</li>
 	<li>Linux Kernel 2.2: Red Hat 6/7.0:
<pre class="code">Time units may be: days, hours, minutes, or seconds 
Grace period before enforcing soft limits for users: 
/dev/hda2: block grace period: 0 days, file grace period: 0 days</pre>
</li>
</ul>
Change the 0 days part to any length of time you feel reasonable. A good choice might be 7 days (or 1 week).

<b>Quota files:</b> (non-XFS file systems)

The edquota command will create/edit the quota file at the root of the file system. (See <tt>/etc/mtab</tt> for the list of the currently mounted filesystems.)
<ul>
 	<li>Version 2: <tt>aquota.user, aquota.group</tt></li>
 	<li>Version 1: <tt>quota.user, quota.group</tt></li>
</ul>
<div class="labelbar">The Linux Kernel:</div>
The default Red Hat/Fedora Core Linux kernel is shipped quota ready. If you have streamlined your kernel by rebuilding it with fewer options, make sure it has been configured with quotas support. When using the tools xconfig or menuconfig be sure to reply y to:
<tt>Quota support (CONFIG_QUOTA) [n] y</tt>

Fedora Core 3: <tt>grep CONFIG_QUOTA /usr/src/redhat/SOURCES/kernel-2.6.9-x86_64.config</tt>
Response:
<pre class="code-box">CONFIG_QUOTA=y
CONFIG_QUOTACTL=y</pre>
The Redhat default init script <tt>/etc/rc.d/rc.sysinit</tt> will also contain a point in the script to run quotacheck:
<ul>
 	<li>Red Hat 6, 7.0:
<pre class="code-box">if [ -x /sbin/quotacheck ]; then
        echo "Checking root filesystem quotas"  
        /sbin/quotacheck -v -a
fi</pre>
And turn quota checking on:
<pre class="code-box">if [ -x /usr/sbin/quotaon ] then
   echo "Turning on quota."
   /usr/sbin/quotaon -v -a
fi

</pre>
</li>
</ul>
<div class="labelbar">Links/Information:</div>
Also note that system limits may be set in the configuration file: <tt>/etc/security/limits.conf</tt>. Here file size limits may be set for core dumps and data files as well as resource limits such as max cpu time and number of processes.

<b>More Quota Info:</b>
<ul>
 	<li><a href="http://www.freebsd.org/handbook/quotas.html">http://www.freebsd.org/handbook/quotas.html</a></li>
</ul>
<b>Software Available From:</b>

<a href="ftp://ftp.funet.fi/pub/Linux/PEOPLE/Linus/subsystems/quota/all.tar.gz">ftp://ftp.funet.fi/pub/Linux/PEOPLE/Linus/subsystems/quota/all.tar.gz</a>

<b>Linux man pages:</b>
<ul>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=quota">quota</a> - display disk usage and limits</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=rquota">rquota</a> - implement quotas on remote machines</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=fstab">fstab</a> - static information about the filesystems</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=edquota">edquota</a> - edit user quotas</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=setquota">setquota</a> - set disk quotas (Command line editor)</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=quotacheck">quotacheck</a> - scan a filesystem for disk usage, create, check and repair quota files</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=quotaon">quotaon</a> - turn filesystem quotas on</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=quotaoff">quotaoff</a> - turn filesystem quotas off</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=repquota">repquota</a> - produce a summary of quota information for a file system</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=convertquota">convertquota</a> - convert quota from old file format to new one. Convert quota.user to aquota.user</li>
 	<li><a href="http://man.yolinux.com/cgi-bin/man2html?cgi_command=quotactl">quotactl</a> - manipulate disk quotas (C programmer interface)</li>
</ul>