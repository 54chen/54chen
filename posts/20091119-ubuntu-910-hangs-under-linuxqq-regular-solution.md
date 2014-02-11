title: ubuntu 9.10下linuxqq经常挂掉的解决方案
link: http://www.54chen.com/life/ubuntu-910-hangs-under-linuxqq-regular-solution.html
author: cc0cc
description: 
post_id: 836
created: 2009/11/19 16:37:58
created_gmt: 2009/11/19 08:37:58
comment_status: open
post_name: ubuntu-910-hangs-under-linuxqq-regular-solution
status: publish
post_type: post

# ubuntu 9.10下linuxqq经常挂掉的解决方案

ubuntu 9.10下linuxqq（官方的QQ，八百年不更新的那个了） 

> sudo vim /usr/bin/qq

增加 

> #!/bin/sh export GDK_NATIVE_WINDOWS=true cd /usr/share/tencent/qq/ ./qq

经试验正确无误。。。linuxqq不再挂。。。五四陈科学院小道报道

## Comments

**[阿政](#13029 "2010-09-07 16:32:19"):** 厉害羡慕的五体投地

**[luu](#13106 "2010-10-14 16:47:14"):** 长胡长会的niu

**[tsung](#12003 "2009-11-20 10:31:17"):** 恩,thx~~

**[蜗牛](#12079 "2009-12-26 22:36:06"):** 牛b的0蛋

**[cc0cc](#12083 "2009-12-28 17:27:13"):** 一起牛B～～

**[owen](#12133 "2010-01-22 00:06:30"):** Good one~~~ Thanks aaaaa lot~~

**[Jim Jin](#12159 "2010-02-23 12:55:41"):** GDK_NATIVE_WINDOWS=true 这个东西不只是解决QQ问题 而是能解决一系列问题 Eclipse, Oracle BPM等等，只要有窗口的程序 在ubuntu 9.10下都可能有问题

