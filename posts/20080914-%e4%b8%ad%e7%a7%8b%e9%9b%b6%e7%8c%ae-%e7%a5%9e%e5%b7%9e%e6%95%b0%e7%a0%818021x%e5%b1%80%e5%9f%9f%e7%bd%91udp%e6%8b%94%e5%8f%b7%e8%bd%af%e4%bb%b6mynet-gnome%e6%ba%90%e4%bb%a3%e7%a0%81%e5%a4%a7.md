title: [中秋零献] 神州数码802.1x局域网UDP拔号软件MyNet-Gnome源代码大分析（Part1）
link: http://www.54chen.com/_linux_/%e4%b8%ad%e7%a7%8b%e9%9b%b6%e7%8c%ae-%e7%a5%9e%e5%b7%9e%e6%95%b0%e7%a0%818021x%e5%b1%80%e5%9f%9f%e7%bd%91udp%e6%8b%94%e5%8f%b7%e8%bd%af%e4%bb%b6mynet-gnome%e6%ba%90%e4%bb%a3%e7%a0%81%e5%a4%a7.html
author: admin
description: 
post_id: 65
created: 2008/09/14 17:05:54
created_gmt: 2008/09/14 09:05:54
comment_status: open
post_name: %e4%b8%ad%e7%a7%8b%e9%9b%b6%e7%8c%ae-%e7%a5%9e%e5%b7%9e%e6%95%b0%e7%a0%818021x%e5%b1%80%e5%9f%9f%e7%bd%91udp%e6%8b%94%e5%8f%b7%e8%bd%af%e4%bb%b6mynet-gnome%e6%ba%90%e4%bb%a3%e7%a0%81%e5%a4%a7
status: publish
post_type: post

# [中秋零献] 神州数码802.1x局域网UDP拔号软件MyNet-Gnome源代码大分析（Part1）

/*** * Author: cc0cc * E-mail: cc0cc@126.com * WebSite: <http://www.54chen.com> * Date: the Mid-Autumn Festival of 2008 ***/ 概要：这个软件是三年前的时候在学校里写的，主要目的是给广大的Linuxers一个上网的机会，不过后来在我推出后官方又出了个Linux下的拔号二进制包。 

PS:神州数码的局域网拔号软件在各大高校使用很多，有不少小区也是，一般官方发布的会有一定的限制。。。

熟悉802.1x协议的过程，对网络编程的学习也有一定的提高。 代码中的ASM汇编部分代码取自北航的高手xdkui（目前在MS就职），在此一并感谢他的努力hack。 声明：本代码只供学习使用，完全遵循计算机软件保护条例。 1.开发环境： gtk+/gnome Anjuta+Glade 2.文件树（截图）： 

![](/wp-content/uploads/2008/11/f53990fba317f23b4f4aeaba.png)

/*** * Author: cc0cc * E-mail: cc0cc@126.com * WebSite: <http://www.54chen.com>

* Date: the Mid-Autumn Festival of 2008 ***/