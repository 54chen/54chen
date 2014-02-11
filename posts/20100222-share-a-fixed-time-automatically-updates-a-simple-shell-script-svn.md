title: 分享一个固定时间自动更新svn的简单shell脚本
link: http://www.54chen.com/_linux_/share-a-fixed-time-automatically-updates-a-simple-shell-script-svn.html
author: cc0cc
description: 
post_id: 966
created: 2010/02/22 14:28:07
created_gmt: 2010/02/22 06:28:07
comment_status: open
post_name: share-a-fixed-time-automatically-updates-a-simple-shell-script-svn
status: publish
post_type: post

# 分享一个固定时间自动更新svn的简单shell脚本

这是一个超级简单的[脚本](/_linux_/zero-90-xian-shell-script-sacrificed-server-batch-operations.html)，旨在提醒观众：在crontab最小精度只能到1分钟的情况下，可以使用这个作到1秒钟的间隔执行时间。 看代码： 

> #!/bin/sh while true do /usr/bin/svn up /data/web/club/ #(指定的要更新的目录) sleep 5 done

原理很简单，利用shell中sleep的单位为秒作自动执行，如果要更加小的精度，估计可以利用这个基础之上多个同时执行:) 仅猜想，标准一些的作法还是用java或者是php的sleep（毫秒级的）。

## Comments

**[lnbsd](#13573 "2011-05-26 10:33:31"):** 晕为啥不用svn的钩子，用这东西

