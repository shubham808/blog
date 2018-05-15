---
layout: post
title: Linux Directory Structure
description: "The meaning and uses of all the directories in the Linux system"
category: linux
tags: []
imagefeature:
comments: true
share: true
---

Linux is a free open source operating system that you can make changes to and redistribute. It is easy for understanding the working of a typical operating system. In this series we shall first look at the directory structure.


### Linux Terminal
The terminal is a program that recieves command from the user and gives it to the OS to process, and it shows the output. It is similar to `command prompt` in Windows. Majority of the tasks undertaken on Linux are through its terminal window.
Shortcut to open a terminal window is: Ctrl+Alt+T


### Understanding the Structure
The Filesystem Hierarchy Standard ![FHS](http://www.pathname.com/fhs/pub/fhs-2.3.html) defines the structure of file systems on Linux and other UNIX-like operating systems. We shall be looking at the directory structure of Ubuntu 16.04. (I have personalised my version a bit, hence the different appearance.)

![directory.png](path)

Before diving into the directory structure, we first understand how Linux understands the files. The FHS classifies files into *shareable/unshareable* and *variable/static*.

**Shareable** files are those that can be stored on one host and used on others.
**Unshareable** files are not sharable.
eg. files in user home directories are shareabale whereas device lock files are not.
*Shareable* and *unshareable* files are segregated because sharing files makes it convenient to use necessary files which are installed on a foreign host by mounting the necessary directories.

**Static** files include binaries, libraries, docuumentation files and other files that do not change without system admin permissions.
**Variable** files are files that are not static.
*Static* and *variable* files are segregated becuase static files can be stored on read-only media and do not need to be backed up on the same schedule as  variable files.

An example of a FHS-compliant system:
<div class="row">
    <div class="large-12 columns">
        <table>
  <thead>
    <tr>
      <th></th>
      <th width="150">Shareable</th>
      <th width="150">Unshareable</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>static</td>
      <td>/usr</td>
      <td>/etc</td>
    </tr>
    <tr>
      <td></td>
      <td>/opt</td>
      <td>/boot</td>
    </tr>
    <tr>
      <td>variable</td>
      <td>/var/mail</td>
      <td>/var/run</td>
    </tr>
    <tr>
      <td></td>
      <td>/var/spool/news</td>
      <td>/var/lock</td>
    </tr>
  </tbody>
</table>
    </div>
</div> -->


### The Filesystem

**/ : the root Directory:**
This is similar to the `C:\` directory on Windows in function. The contents of this directory must be sufficient to boot, restore, recover and/or repair the system.

The following directories are required in `/ ` as per FHS.
<div class="row">
    <div class="large-12 columns">
        <table>
  <thead>
    <tr>
      <th>Directory</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>bin</td>
      <td>Essential command binaries</td>
    </tr>
    <tr>
      <td>boot</td>
      <td>Static files of the bootloader</td>
    </tr>
    <tr>
      <td>dev</td>
      <td>Device files</td>
    </tr>
    <tr>
      <td>etc</td>
      <td>Host specific system configuration</td>
    </tr>
    <tr>
      <td>lib</td>
      <td>Essenyial shared libraries and kernel modules</td>
    </tr>
    <tr>
      <td>media</td>
      <td>Mount point for removable media</td>
    </tr>
    <tr>
      <td>mnt</td>
      <td>Mount point for mounting a filesystem temporarily</td>
    </tr>
    <tr>
      <td>opt</td>
      <td>Add-on application software packages</td>
    </tr>
    <tr>
      <td>sbin</td>
      <td>Essential system binaries</td>
    </tr>
    <tr>
      <td>srv</td>
      <td>Data for services provided by this system</td>
    </tr>
    <tr>
      <td>tmp</td>
      <td>Temporary files</td>
    </tr>
    <tr>
      <td>usr</td>
      <td>Secondary hierarchy</td>
    </tr>
    <tr>
      <td>var</td>
      <td>Variable data</td>
    </tr>
    <tr>
      <td>home</td>
      <td>User home directories (optional)</td>
    </tr>
    <tr>
      <td>lib</td>
      <td>Alternate format essential shared libraries (optional)</td>
    </tr>
    <tr>
      <td>root</td>
      <td>Home directory for the root user (optional)</td>
    </tr>    

  </tbody>
</table>
    </div>
</div> -->


Every single file and directory has a path beginning from root directory i.e. path of folder bin is `/bin`. Only the root user has the privilege to modify files in this directory i.e. use `sudo`.


2. **/bin : essential user binaries:**
 It contains all the programs(binaries) that must be present when the system is mounted in single-user mode.

 ![/bin.jpg](path)

 * There must be no subdirectories in `/bin`
 * Commands used by all the users are located here (eg. chmod, cp, bash, ls)
 * Applications such as Firefox are stored in `/usr/bin`
 * A similar directory, `/sbin` contains essential system administration binaries (eg iptables, ifconfig, fdisk, reboot)


3. **/boot : static boot files:**
It contains boot loader related files like GRUB files and Linux kernels i.e. the files needed to boot the system. The boot loader's configuration files are located in `/etc` which stores configuration files which we shall see further.

![/boot.jpg](path)

* kernel files: initrd.img-*
* grub files: inside grub folder
* vmlinux: vmlinuz-*


4. **/cdrom : historical mount point for CD-ROMs:**
It isn't part of FHS standard, yet finds its way into Ubuntu and other distributions. It is a temporary location for CD-ROMs inserted in the system.
Standard location for *all* temporary media is in `/media` directory.


5. **/dev : device files:**
These files as the name suggests, are files which represent actual devices. These are not *files* as we may assume.  they include terminal devices,usb,any connected device,or even pseudo devices-which are virtual devices that do not correspond to to hardware. An exampe of pseudo device is- `/dev/null` is a special device that produces no output and discars all input.


6. **/etc : configuration files:**

7. **/home :files:**
8. **/lib :files:**
9. **/lost+found :files:**
10. **/media :files:**
11. **/mnt :files:**
12. **/opt :files:**
13. **/proc :files:**
14. **/root :files:**
15. **/run :files:**
16. **/sbin :files:**
17. **/srv :files:**
18. **/tmp :files:**
19. **/usr :files:**
20. **/var :files:**


<!-- ## Tables

<div class="row">
    <div class="large-12 columns">
        <table>
  <thead>
    <tr>
      <th width="200">Table Header</th>
      <th>Table Header</th>
      <th width="150">Table Header</th>
      <th width="150">Table Header</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Content Goes Here</td>
      <td>This is longer content Donec id elit non mi porta gravida at eget metus.</td>
      <td>Content Goes Here</td>
      <td>Content Goes Here</td>
    </tr>
    <tr>
      <td>Content Goes Here</td>
      <td>This is longer Content Goes Here Donec id elit non mi porta gravida at eget metus.</td>
      <td>Content Goes Here</td>
      <td>Content Goes Here</td>
    </tr>
    <tr>
      <td>Content Goes Here</td>
      <td>This is longer Content Goes Here Donec id elit non mi porta gravida at eget metus.</td>
      <td>Content Goes Here</td>
      <td>Content Goes Here</td>
    </tr>
  </tbody>
</table>
    </div>
</div> -->

<!-- ## Code Snippets

Syntax highlighting via Pygments

{% highlight css %}
#container {
  float: left;
  margin: 0 -240px 0 0;
  width: 100%;
}
{% endhighlight %}

## Buttons

Make any link standout more when applying the `button` class.

<a href="#" class="button">Default Button</a> -->
