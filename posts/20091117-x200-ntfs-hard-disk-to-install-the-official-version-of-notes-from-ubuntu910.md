title: x200 ntfs硬盘安装ubuntu9.10正式版手记
link: http://www.54chen.com/_linux_/x200-ntfs-hard-disk-to-install-the-official-version-of-notes-from-ubuntu910.html
author: cc0cc
description: 
post_id: 830
created: 2009/11/17 13:42:06
created_gmt: 2009/11/17 05:42:06
comment_status: open
post_name: x200-ntfs-hard-disk-to-install-the-official-version-of-notes-from-ubuntu910
status: publish
post_type: post

# x200 ntfs硬盘安装ubuntu9.10正式版手记

09年10月31日，9.10发布了正式版本，科学院曾经出过两篇硬盘安装的手记，一个是8的一个是9.10测试版本的。地址是： [[十一归来]ubuntu 9.10 硬盘安装手记](/796-11-return-ubuntu-910-notes-from-the-hard-disk-installation/) [ubuntu 8.10 desktop版本 硬盘安装手记](http://www.54chen.com/372-ubuntu-810-desktop%e7%89%88%e6%9c%ac-%e7%a1%ac%e7%9b%98%e5%ae%89%e8%a3%85%e6%89%8b%e8%ae%b0/) 先说平时我们用wingrub安装的时候，是不支持ntfs的，只能用grub for dos的grldr文件(grub4dos的这个文件支持ntfs)。 下载iso文件：ubuntu-9.10-desktop-i386.iso 同上一文，我列出使用的boot.ini和menu.lst: boot.ini 

> [boot loader] timeout=30 default=multi(0)disk(0)rdisk(0)partition(1)\WINDOWS [operating systems] multi(0)disk(0)rdisk(0)partition(1)\WINDOWS="Microsoft Windows XP Professional" /noexecute=optin /fastdetect C:\GRLDR="wingrub"

menu.lst 

> timeout 10 title Windows at (hd0,0) root (hd0,0) chainloader +1 title install Ubuntu9.10 root (hd0,0) kernel (hd0,0)/vmlinuz boot=casper iso-scan/filename=/ubuntu-9.10-desktop-i386.iso ro quiet splash – label check live-install initrd (hd0,0)/initrd.lz

然后，iso文件放在D盘，用RAR打开iso文件，解压出来initrd.lz和vmlinuz两个文件。 重启电脑，进入desktop，双击安装。 最后的时候运行一下umount -l /isodevice 安装结束后，重启进系统，中文不全，选择一下最快的源，联网下语言包。 thinpad x200 所有硬件全部正常可使用。

## Comments

**[zwws](#12001 "2009-11-19 12:58:28"):** 实在不敢在Thinkpad上装Ubuntu了, 我的R400装上后发热量瞬增...

**[cc0cc](#12002 "2009-11-19 13:37:15"):** 经我的测试，x200没发现啥异常，很冷很静电量正常

