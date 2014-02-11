title: ubuntu10.04 下通过usb在G7（desire）上真机调试android程序的设置
link: http://www.54chen.com/java-ee/ubuntu10-04-g7-desire-android-dev.html
author: 54chen
description: 
post_id: 1533
created: 2010/12/27 14:25:56
created_gmt: 2010/12/27 06:25:56
comment_status: open
post_name: ubuntu10-04-g7-desire-android-dev
status: publish
post_type: post

# ubuntu10.04 下通过usb在G7（desire）上真机调试android程序的设置

![android](http://img03.taobaocdn.com/imgextra/i3/T1XlxVXeByXXbYuqZW_022510.jpg) 本文为android开发入门系列，以下是各节传送门： android 开发入门 <http://www.54chen.com/_linux_/androiddev.html> android开发入门2：概念建立 <http://www.54chen.com/_linux_/androiddev2.html> 一、工具介绍 android-sdk-linux_x86/tools下有一堆工具，今天要用的是：Android Debug Bridge，在android开发过程中，这个工具是使用得最多的。（缩写adb，有点像gcc的gdb似的） 

> adb start-server - 实际上它会启动一个 adb fork-server server adb kill-server - kill掉 adb devices - 列出所有的设备

二、设置usb权限 因为ubuntu这样的系统都是默认以非root身份在运行的，要使用usb调试，需要sudo支持。 

> $ lsusb Bus 005 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub Bus 004 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub Bus 003 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub Bus 002 Device 003: ID 413c:2106 Dell Computer Corp. Bus 002 Device 002: ID 0461:4d81 Primax Electronics, Ltd Bus 002 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub Bus 001 Device 010: ID 0bb4:0c87 High Tech Computer Corp. Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

列表中，Bus 001 Device 010: ID 0bb4:0c87 High Tech Computer Corp. 这一行为htc手机的usb使用端口，记录一下，id为0bb4（基于上所有的htc都是这个ID）。 

> $sudo vim /etc/udev/rules.d/70-android.rules

加入以下内容： 

> SUBSYSTEM=="usb", SYSFS{idVendor}=="0bb4", MODE="0666" 

运行命令，重启udev： 

> $sudo chmod a+rx /etc/udev/rules.d/70-android.rules $sudo service udev restart 

三、重新启动adb server (很重要)拔掉usb重新连上再执行： 

> sudo ./adb kill-server ./adb devices ./adb root (这一步很重要 感谢小米科技小军献策)

四、使用usb进行调试 eclispe>run as>android application 选择真实机器。