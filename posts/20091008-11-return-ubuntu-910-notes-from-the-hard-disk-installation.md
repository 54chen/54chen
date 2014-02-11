title: [十一归来]ubuntu 9.10 硬盘安装手记
link: http://www.54chen.com/_linux_/11-return-ubuntu-910-notes-from-the-hard-disk-installation.html
author: cc0cc
description: 
post_id: 796
created: 2009/10/08 22:57:26
created_gmt: 2009/10/08 14:57:26
comment_status: open
post_name: 11-return-ubuntu-910-notes-from-the-hard-disk-installation
status: publish
post_type: post

# [十一归来]ubuntu 9.10 硬盘安装手记

很早之前，科学院发布过一篇 [ubuntu 8.10 desktop版本 硬盘安装手记](/372-ubuntu-810-desktop%e7%89%88%e6%9c%ac-%e7%a1%ac%e7%9b%98%e5%ae%89%e8%a3%85%e6%89%8b%e8%ae%b0/) 时隔今日，中间经过了一个9.04的beta版本后，又迎来了新的beta版本 9.10最近的发布过程： 2009年10月1日——Beta版本发布 2009年10月22日——公布发布候选版本 2009年10月29日——Ubuntu 9.10正版版本上架 根据总结，Ubuntu 9.10中将会具有以下全新特性： 1、Ubuntu 9.10中将会添加全新的令人印象更加深刻的启动图形； 2、Ubuntu 9.10将会具有更快的启动速度（“Jaunty Jackalope”曾经在netbook上实现25秒的启动速度，Karmic Koala，尽管里面的动物“树袋熊”速度比较慢，但有望实现更快的启动速度）； 3、Ubuntu 9.10将可以为Netbook更好地整合Moblin技术； 4、Ubuntu 9.10将具有更好的视觉体验； 5、Ubuntu 9.10将通过完整的开源软件来提升服务器版本的云计算性能； 6、Ubuntu 9.10将主要采用Amazon APIs来管理云计算，当然GoGrid APIs 也在考虑中； 7、Ubuntu 9.10中的Canonical 将创建Karmic Koala的标准Amazon机器镜像，本质上就是创建"ready-to-run" 程序，作为Amazon群体的“标准版本”； 8、为了节约资源，Ubuntu 9.10中的休眠和唤醒特性将扩展到服务器版本。 

> 第七这个太牛B了，单台机器做成云里的一个节点，绝对主流，绝对未来！力顶！

7号，休假归来，拖一回搞个系统玩。 以下是安装步骤记录： **下载：** <http://releases.ubuntu.com/karmic/ubuntu-9.10-beta-desktop-i386.iso> 用迅雷啥的来拖，速度还不错，半小时搞到手。 改名为ubuntu9.iso，放在了D：根目录里，用winrar打开，解压casper文件下的两个文件到c盘根目录。 vmlinuz   和 initrd.lz **winGrub配置：** 很高兴这个老机器安装了一个fedora core5和win2k，n年前的系统，从来不换系统，有毒杀毒，有病治病。 winGrub的安装见 [ubuntu 8.10 desktop版本 硬盘安装手记](/372-ubuntu-810-desktop%e7%89%88%e6%9c%ac-%e7%a1%ac%e7%9b%98%e5%ae%89%e8%a3%85%e6%89%8b%e8%ae%b0/) 前半部分内容。 所以系统中已经有一个之前的GRLDR了，现在抄一下boot.ini和menu.lst C:\boot.ini 

> [boot loader] timeout=5 default=multi(0)disk(0)rdisk(0)partition(1)\WINNT [operating systems] multi(0)disk(0)rdisk(0)partition(1)\WINNT="Microsoft Windows 2000 Professional" /fastdetect C:\GRLDR="Linux"

C:\boot\grub\menu.lst 

> timeout 15 title 1. install Ubuntu9.10 root (hd0,0) kernel (hd0,0)/vmlinuz boot=casper iso-scan/filename=/ubuntu9.iso ro quiet splash -- label check live-install initrd (hd0,0)/initrd.lz

**进入liveCD安装：** 重启，先从boot.ini的界面进linux，再从wingrub的界面进 install Ubuntu9.10 一切正常的话，能直接进入LiveCD的桌面了。 然后打开终端，运行： 

> umount -l /isodevice

千万不要运行：umount -l /cdrom(真的不知道是哪个SB传播的要下这个区，这会导致之后的grub里看不到你装好的ubuntu系统) 双击桌面的安装。。。 这台老机器，2个80G的硬盘，全都使用了97%左右的空间（鬼知道装了些什么，难道有过量的毛片），每次到第四步识别分区的时候都要花12分钟左右。 **总结：** 这个版本的界面美化做得不错，不过grub2没感觉到有多方便，有可能是为了提高开机速度吧。目前为止，中文包不全，很多地方没翻译完。 用QQ for linux 和IBUS的输入法有冲突的感觉，老是动不动挂掉。 版本太新，firefox啥的找个flash插件都困难。 要试的同志，还是再等半个月正式发布。

## Comments

**[Jessica](#11916 "2009-10-13 17:36:57"):** 以前折腾过MAC..分区 借了移动硬盘 麻烦死了 结果好不容易安装好 没有显卡 网卡 无线网卡 声卡驱动 无奈..

