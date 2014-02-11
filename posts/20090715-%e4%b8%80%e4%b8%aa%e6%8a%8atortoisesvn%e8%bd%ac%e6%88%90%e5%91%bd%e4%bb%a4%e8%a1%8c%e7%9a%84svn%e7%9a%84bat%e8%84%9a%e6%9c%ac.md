title: 一个把TortoiseSVN转成命令行的svn的bat脚本
link: http://www.54chen.com/architecture/%e4%b8%80%e4%b8%aa%e6%8a%8atortoisesvn%e8%bd%ac%e6%88%90%e5%91%bd%e4%bb%a4%e8%a1%8c%e7%9a%84svn%e7%9a%84bat%e8%84%9a%e6%9c%ac.html
author: cc0cc
description: 
post_id: 632
created: 2009/07/15 17:21:31
created_gmt: 2009/07/15 09:21:31
comment_status: open
post_name: %e4%b8%80%e4%b8%aa%e6%8a%8atortoisesvn%e8%bd%ac%e6%88%90%e5%91%bd%e4%bb%a4%e8%a1%8c%e7%9a%84svn%e7%9a%84bat%e8%84%9a%e6%9c%ac
status: publish
post_type: post

# 一个把TortoiseSVN转成命令行的svn的bat脚本

TortoiseSVN是windows里常用的svn客户端了，有些IDE（比如说Zend Studio）要设置svn.exe的地址才能绑上svn来用，一般情况下，他附带的都是很古老的版本。 把下面的脚本保存为svn.bat，再在[ide](/c/406)里设置svn客户端为这个bat文件，很好用 

> @ECHO OFF rem This is a svn for IDE rem from http://www.54chen.com start "TortoiseSVN" "C:\Program Files\TortoiseSVN\bin\TortoiseProc.exe" /notempfile /command:%1 /path:%2

## Comments

**[Ven](#10657 "2009-07-16 11:12:36"):** Zend Studio至少从6.1开始就自带了一个svn客户端，不需设置svn.exe。

**[cc0cc](#10658 "2009-07-16 13:29:22"):** :) 这个脚本也可以在其他ide里使用 只是个替代方案而已

