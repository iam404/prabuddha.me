---
id: 666
title: Strace Examples to Debug the Execution of a Program in Linux
date: 2018-03-26T21:03:57+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=666
permalink: /strace-examples-to-debug-the-execution-of-a-program-in-linux/
categories:
  - linux
---
Strace is a debugging tool that will help you troubleshoot issues.

Strace monitors the system calls and signals of a specific program. It is helpful when you do not have the source code and would like to debug the execution of a program. strace provides you the execution sequence of a binary from start to end.

This article explains few strace examples to get you started.
<span id="more-8965"></span>
<h3>1. Trace the Execution of an Executable</h3>
You can use strace command to trace the execution of any executable. The following example shows the output of strace for the Linux ls command.
<pre>$  strace ls
execve("/bin/ls", ["ls"], [/* 21 vars */]) = 0
brk(0)                                  = 0x8c31000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
mmap2(NULL, 8192, PROT_READ, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb78c7000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY)      = 3
fstat64(3, {st_mode=S_IFREG|0644, st_size=65354, ...}) = 0
...
...
...</pre>
<h3>2. Trace a Specific System Calls in an Executable Using Option -e</h3>
Be default, strace displays all system calls for the given executable. To display only a specific system call, use the strace -e option as shown below.
<pre>$ strace -e open ls
open("/etc/ld.so.cache", O_RDONLY)      = 3
open("/lib/libselinux.so.1", O_RDONLY)  = 3
open("/lib/librt.so.1", O_RDONLY)       = 3
open("/lib/libacl.so.1", O_RDONLY)      = 3
open("/lib/libc.so.6", O_RDONLY)        = 3
open("/lib/libdl.so.2", O_RDONLY)       = 3
open("/lib/libpthread.so.0", O_RDONLY)  = 3
open("/lib/libattr.so.1", O_RDONLY)     = 3
open("/proc/filesystems", O_RDONLY|O_LARGEFILE) = 3
open("/usr/lib/locale/locale-archive", O_RDONLY|O_LARGEFILE) = 3
open(".", O_RDONLY|O_NONBLOCK|O_LARGEFILE|O_DIRECTORY|O_CLOEXEC) = 3
Desktop  Documents  Downloads  examples.desktop  libflashplayer.so 
Music  Pictures  Public  Templates  Ubuntu_OS  Videos</pre>
The above output displays only the open system call of the ls command. At the end of the strace output, it also displays the output of the ls command.

If you want to trace multiple system calls use the “-e trace=” option. The following example displays both open and read system calls.
<pre>$ strace -e trace=open,read ls /home
open("/etc/ld.so.cache", O_RDONLY)      = 3
open("/lib/libselinux.so.1", O_RDONLY)  = 3
read(3, "\177ELF\1\1\1\3\3\1\260G004"..., 512) = 512
open("/lib/librt.so.1", O_RDONLY)       = 3
read(3, "\177ELF\1\1\1\3\3\1\300\30004"..., 512) = 512
..
open("/lib/libattr.so.1", O_RDONLY)     = 3
read(3, "\177ELF\1\1\1\3\3\1\360\r004"..., 512) = 512
open("/proc/filesystems", O_RDONLY|O_LARGEFILE) = 3
read(3, "nodev\tsysfs\nnodev\trootfs\nnodev\tb"..., 1024) = 315
read(3, "", 1024)                       = 0
open("/usr/lib/locale/locale-archive", O_RDONLY|O_LARGEFILE) = 3
open("/home", O_RDONLY|O_NONBLOCK|O_LARGEFILE|O_DIRECTORY|O_CLOEXEC) = 3
bala</pre>
<h3>3. Save the Trace Execution to a File Using Option -o</h3>
The following examples stores the strace output to output.txt file.
<pre>$ strace -o output.txt ls
Desktop  Documents  Downloads  examples.desktop  libflashplayer.so
Music  output.txt  Pictures  Public  Templates  Ubuntu_OS  Videos

$ cat output.txt 
execve("/bin/ls", ["ls"], [/* 37 vars */]) = 0
brk(0)                                  = 0x8637000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
mmap2(NULL, 8192, PROT_READ, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7860000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY)      = 3
fstat64(3, {st_mode=S_IFREG|0644, st_size=67188, ...}) = 0
...
...</pre>
<h3>4. Execute Strace on a Running Linux Process Using Option -p</h3>
You could execute strace on a program that is already running using the process id. First, identify the PID of a program using <a href="https://www.thegeekstuff.com/2011/04/ps-command-examples/">ps command</a>.

For example, if you want to do strace on the firefox program that is currently running, identify the PID of the firefox program.
<pre>$ ps -C firefox-bin
  PID TTY          TIME CMD
 1725 ?        00:40:50 firefox-bin</pre>
Use strace -p option as shown below to display the strace for a given process id.
<pre>$  sudo strace -p 1725 -o firefox_trace.txt

$ tail -f firefox_trace.txt</pre>
Now the execution trace of firefox process will be logged into firefox_trace.txt text file. You can tail this text file to watch the live trace of the firefox executable.

Strace will display the following error when your user id does not match the user id of the given process.
<pre>$  strace -p 1725 -o output.txt
attach: ptrace(PTRACE_ATTACH, ...): Operation not permitted
Could not attach to process.  If your uid matches the uid of the target
process, check the setting of /proc/sys/kernel/yama/ptrace_scope, or try
again as the root user.  For more details, see /etc/sysctl.d/10-ptrace.conf</pre>
<h3>5. Print Timestamp for Each Trace Output Line Using Option -t</h3>
To print the timestamp for each strace output line, use the option -t as shown below.
<pre>$ strace -t -e open ls /home
20:42:37 open("/etc/ld.so.cache", O_RDONLY) = 3
20:42:37 open("/lib/libselinux.so.1", O_RDONLY) = 3
20:42:37 open("/lib/librt.so.1", O_RDONLY) = 3
20:42:37 open("/lib/libacl.so.1", O_RDONLY) = 3
20:42:37 open("/lib/libc.so.6", O_RDONLY) = 3
20:42:37 open("/lib/libdl.so.2", O_RDONLY) = 3
20:42:37 open("/lib/libpthread.so.0", O_RDONLY) = 3
20:42:37 open("/lib/libattr.so.1", O_RDONLY) = 3
20:42:37 open("/proc/filesystems", O_RDONLY|O_LARGEFILE) = 3
20:42:37 open("/usr/lib/locale/locale-archive", O_RDONLY|O_LARGEFILE) = 3
20:42:37 open("/home", O_RDONLY|O_NONBLOCK|O_LARGEFILE|O_DIRECTORY|O_CLOEXEC) = 3
bala</pre>
<h3>6. Print Relative Time for System Calls Using Option -r</h3>
Strace also has the option to print the execution time for each system calls as shown below.
<pre>$ strace -r ls 
     0.000000 execve("/bin/ls", ["ls"], [/* 37 vars */]) = 0
     0.000846 brk(0)                    = 0x8418000
     0.000143 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory)
     0.000163 mmap2(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb787b000
     0.000119 access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory)
     0.000123 open("/etc/ld.so.cache", O_RDONLY) = 3
     0.000099 fstat64(3, {st_mode=S_IFREG|0644, st_size=67188, ...}) = 0
     0.000155 mmap2(NULL, 67188, PROT_READ, MAP_PRIVATE, 3, 0) = 0xb786a000
     ...
     ...</pre>
<h3>7. Generate Statistics Report of System Calls Using Option -c</h3>
Using option -c, strace provides useful statistical report for the execution trace. The “calls” column in the following output indicated how many times that particular system call was executed.
<pre>$ strace -c ls /home
bala
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
  -nan    0.000000           0         9           read
  -nan    0.000000           0         1           write
  -nan    0.000000           0        11           open
  -nan    0.000000           0        13           close
  -nan    0.000000           0         1           execve
  -nan    0.000000           0         9         9 access
  -nan    0.000000           0         3           brk
  -nan    0.000000           0         2           ioctl
  -nan    0.000000           0         3           munmap
  -nan    0.000000           0         1           uname
  -nan    0.000000           0        11           mprotect
  -nan    0.000000           0         2           rt_sigaction
  -nan    0.000000           0         1           rt_sigprocmask
  -nan    0.000000           0         1           getrlimit
  -nan    0.000000           0        25           mmap2
  -nan    0.000000           0         1           stat64
  -nan    0.000000           0        11           fstat64
  -nan    0.000000           0         2           getdents64
  -nan    0.000000           0         1           fcntl64
  -nan    0.000000           0         2         1 futex
  -nan    0.000000           0         1           set_thread_area
  -nan    0.000000           0         1           set_tid_address
  -nan    0.000000           0         1           statfs64
  -nan    0.000000           0         1           set_robust_list
------ ----------- ----------- --------- --------- ----------------
100.00    0.000000                   114        10 total</pre>