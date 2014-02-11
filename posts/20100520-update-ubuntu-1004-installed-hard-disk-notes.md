title: [更新]ubuntu 10.04 硬盘安装手记
link: http://www.54chen.com/_linux_/update-ubuntu-1004-installed-hard-disk-notes.html
author: 54chen
description: 
post_id: 1157
created: 2010/05/20 14:46:44
created_gmt: 2010/05/20 06:46:44
comment_status: open
post_name: update-ubuntu-1004-installed-hard-disk-notes
status: publish
post_type: post

# [更新]ubuntu 10.04 硬盘安装手记

![技术娱乐](http://img07.taobaocdn.com/bao/uploaded/i7/T1CCxAXlthXXcwnIIY_025651.jpg) 在过去的两年里，ubuntu发展迅速，快速成为了技术人员居家工作旅行必备系统。54chen从8.04开始从fc系列（RH的后续个人系统）换到ubuntu来使用，期间连续发布了多篇使用ubuntu各个版本的安装和使用手记。54chen作为互联网最早的ubuntu"义务推广员"，一路看到了ubuntu队伍的发展和壮大。 安装的记录： [ubuntu 8.10 desktop版本 硬盘安装手记](/372-ubuntu-810-desktop%e7%89%88%e6%9c%ac-%e7%a1%ac%e7%9b%98%e5%ae%89%e8%a3%85%e6%89%8b%e8%ae%b0/) [[十一归来]ubuntu 9.10 硬盘安装手记](http://www.54chen.com/_linux_/11-return-ubuntu-910-notes-from-the-hard-disk-installation.html) 还有许多配置相关的手记。 10.04的发布：2010年4月29日(看上去是这样的，四月份发布的都是长期支持的版本，后缀里有一个LTS的标识，同时4月份出的版本往往也是没有啥巨大动作的。) 根据官方总结，Ubuntu 10.04中将会具有以下全新特性： 快速启动：在几乎所有机型上的启动速度都明显更快，在使用固态硬盘(SSD)的笔记本电脑或者上网本等机型上的速度超快。这意味着用户可以开机后立即进入浏览器，快速访问网站。 　　 起点社交：Ubuntu 10.04LTS桌面版新的“Me Menu”(我的菜单)整合了社交网站如Facebook、Digg、Twitter和Identica，使得各个网站的登录和个人信息更新一次完成。 Me Menu的聊天软件，支持同时登录QQ、MSN、Google Talk等多个应用程序，和朋友或者同事聊天。（没有出现本土化的SNS和微博，墙干净了，没啥用） 　　Ubuntu One：增强的在线服务桌面整合功能意味着用户可以更容易地使用云端服务存储、分享和同步文件及文件夹、浏览器书签和通讯录，使得从个人计算机向个人计算应用的演进更进一步。 　　Ubuntu One音乐库：Ubuntu用户可直接使用默认音乐播放器试听全球巨星和著名乐队的作品。用户购买的乐曲将会直接存储在云端的Ubuntu One存储空间中，并可在自己的多台电脑和音乐播放设备上调用和播放。(限无数字版权管理DRM-Free的音乐) 　　Ubuntu软件中心2.0：使用界面更加简洁友好，轻松完成寻找、安装新的应用程序以及查看和删除已安装程序的过程。用户还可直观辨别来自 Ubuntu、Canonical合作伙伴或者使用 Canonical Launchpad PPA服务的开发人员的所提供的软件。 　　Ubuntu 10.04 上网本版(UNE)：除了标准版本拥有的新功能，上网本用户还能在基于固态硬盘(SSD)设备上享受到超快的启动速度，更快的挂起/恢复速度可以进一步延长电池使用时间，当然还包括针对这些小屏幕定制的业界领先的界面。 与9.10相比，Amazon机器镜像的事情没有被继续做深，期待10.10（十全十美）的版本中的表现。 以下是安装步骤记录： **下载：** <http://releases.ubuntu.com/lucid/> 大小600M左右，提示一下下，如果有个2G的U盘啥的，用U盘来安装可以省去很多事情（装进U盘，从U盘启动即可安装）。 改名为ubuntu10.iso，放在了D：根目录里，用winrar打开，解压casper文件下的两个文件到c盘根目录。 vmlinuz   和 initrd.lz **winGrub配置：** 很高兴这个老机器安装了一个fedora core5和win2k，n年前的系统，从来不换系统，有毒杀毒，有病治病。 winGrub的安装见 [ubuntu 8.10 desktop版本 硬盘安装手记](http://www.54chen.com/372-ubuntu-810-desktop%e7%89%88%e6%9c%ac-%e7%a1%ac%e7%9b%98%e5%ae%89%e8%a3%85%e6%89%8b%e8%ae%b0/) 前半部分内容。 所以系统中已经有一个之前的GRLDR了，现在抄一下boot.ini和menu.lst C:\boot.ini 

> [boot loader] timeout=5 default=multi(0)disk(0)rdisk(0)partition(1)\WINNT [operating systems] multi(0)disk(0)rdisk(0)partition(1)\WINNT=”Microsoft Windows 2000 Professional” /fastdetect C:\GRLDR=”Linux”

C:\boot\grub\menu.lst 

> timeout 15 title 1. install Ubuntu10.04 root (hd0,0) kernel (hd0,0)/vmlinuz boot=casper iso-scan/filename=/ubuntu10.iso ro quiet splash – label check live-install initrd (hd0,0)/initrd.lz

**进入liveCD安装：** 重启，先从boot.ini的界面进linux，再从wingrub的界面进 install Ubuntu10.04 一切正常的话，能直接进入LiveCD的桌面了。 然后打开终端，运行： 

> umount -l /isodevice

双击桌面的安装。。。 这台老机器，2个80G的硬盘，全都使用了97%左右的空间（鬼知道装了些什么，难道有过量的毛片），每次到第四步识别分区的时候都要花12分钟左右。 **总结：** liveCD一般都中文包不全，很多地方没翻译完，需要更新。 网速如果不够快，79%进度时最好拔掉网线，因为配置apt时间会过长而不往下面进行。 没有大的惊喜，期待10.10

## Comments

**[大大的小蜗牛](#13164 "2010-11-03 02:36:32"):** 现在我的电脑就是双系统。不过还是winxp用着顺手。习惯了。

**[54chen](#13166 "2010-11-03 10:04:37"):** ubuntu用习惯了也挺顺手的，不会有360和qq在上面打仗

**[cfc4n](#12357 "2010-05-20 15:16:31"):** 陈哥您沿袭了Cnbeta的配图风格。。。

**[54chen](#12362 "2010-05-21 08:59:36"):** 应该说这是一种技术人员的自娱自乐风格 :)

**[FreeUM](#12373 "2010-05-23 00:56:04"):** 玩Ubuntu还是在大学时候在虚拟机上玩的，看来文章后，蠢蠢欲动，有时间再去耍耍

