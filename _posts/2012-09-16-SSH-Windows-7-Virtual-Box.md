---
layout: post
title: "Setting up SSH to a Windows 7 virtual box"
category:
tags: []
---
{% include JB/setup %}

I Learned these first steps from the most reliable source ever.
For a step by step video.
http://www.youtube.com/watch?v=HoSebbqBQQo

I found another guide while writing this that explains the second part
http://www.howtogeek.com/howto/41560/how-to-get-ssh-command-line-access-to-windows-7-using-cygwin/

# Making The Box
* Download VirtualBox ( https://www.virtualbox.org/wiki/Downloads )
* Get an ISO of Windows 7 ( http://www.youtube.com/watch?v=HoSebbqBQQo )
* In VirtualBox create a new machine after the Windows 7 Template.
* Use ICH9 and Enable IO APIC, Otherwise the box segfaults on boot.
* Mount the Windows 7 ISO as an IDE DVD drive. It will not detect it as SATA
* Install windows 7

# Getting SSH Working
* Download Cygwin ( http://cygwin.com/install.html )
* Install OpenSSH via Cygwin
* Run ssh-host-configas Adminstrator. This will make a new user account to run SSHd as a windows service.

# Getting MSVC working
* Download and install Visual Studio 2010
* Source this shell script in your cygwin .bashrc https://gist.github.com/3717601

Success

