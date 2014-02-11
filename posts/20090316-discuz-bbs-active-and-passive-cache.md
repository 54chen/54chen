title: [原创]Discuz! BBS的主动缓存和被动缓存
link: http://www.54chen.com/php-tech/discuz-bbs-active-and-passive-cache.html
author: cc0cc
description: 
post_id: 505
created: 2009/03/16 22:50:07
created_gmt: 2009/03/16 14:50:07
comment_status: open
post_name: discuz-bbs-active-and-passive-cache
status: publish
post_type: post

# [原创]Discuz! BBS的主动缓存和被动缓存

[文章作者：陈臻 本文版本：v1.1 最后修改：2009.3.16 转载请注明原文链接：<http://www.54chen.com/c/505>] 

DZ的缓存同样分了主动缓存和被动缓存。从功能上来说，主动缓存一般用到管理员对全站的设置，等等需要手动更新的地方，这些地方的数据都有一个特点，那就是它们的更新可能性很小，平时不需要自动更新；DZ的被动缓存，一般分布在诸如帖子内容显示，用户信息更新这些地方，这些地方的更新基本上都是因为用户使用了某一特定的功能时所激发的。

在正常运行的DZ系统文件夹里面会有一个forum[data](/c/399)文件夹，这个是论坛记录和缓存文件的存放目录，一般这些文件都是自动生成的，在forumdata/cache/里面存储的都是一些DZ的基本设置和一些常使用的值，这些值一般在系统初始化的时候就保存在$_DCACHE全局变量中，在后面的操作中将可以简单地使用它们进行功能上的判断。

（1）主动缓存，也就是只在用户操作后台时，由DZ系统去删除原有缓存进行更新的缓存。它们普遍存在于forumdata/cache/目录中，比如说/forumdata/cache/cache_settings.php保存了整个系统的核心设置，一般情况是不会更新的，只有后台修改了比如站点名称等关键信息的时候才会去主动更新这个缓存文件。还有用于保存用户组和管理员组相关信息的两类缓存文件：

/forumdata/cache/usergroup_'.intval($groupid).'.php

/forumdata/cache/admingroup_'.intval($adminid).'.php

## Comments

**[小飞](#226 "2009-03-17 08:01:25"):** good，上班来顶一下

**[疏影](#231 "2009-03-17 17:12:50"):** 你对这也有研究……

**[cc0cc](#234 "2009-03-17 22:02:02"):** 经典的东西 当然要研究

**[网中浪子](#1101 "2009-04-15 11:02:54"):** 你写的东西很不错哦，想和你聊聊哦

