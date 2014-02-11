title: java log4j 日志分析工具分享
link: http://www.54chen.com/java-ee/java-log4j-logger-analyzer.html
author: 54chen
description: 
post_id: 1747
created: 2011/06/24 14:26:59
created_gmt: 2011/06/24 06:26:59
comment_status: open
post_name: java-log4j-logger-analyzer
status: publish
post_type: post

# java log4j 日志分析工具分享

![log4j,analyzer](http://img01.taobaocdn.com/imgextra/i1/T1jH1hXjFkXXciRrcW_022533.jpg) java线上项目往往有许多的exception出现了没来得及去看，通过下面的工具，可以在每天早晨上班的时候通过邮件发出头一天出现的异常次数，是提高生产力和提早发现问题的好办法。 配置： 

> 1.解压到指定目录 /data/analyze 2.email发送配置 vim mail.pl 5行，6行，7行，14行 修改为自己的配置 3.server配置 ssh-keygen打通ssh cd server 项目名_server写服务器ip，多个机器一行一个 4.其他 可能会有不相同的log目录地址，看着修改一下shell就行。 

*修改自烧饼的脚本，点击下载： [http://ishare.iask.sina.com.cn/f/16530420.html]( http://ishare.iask.sina.com.cn/f/16530420.html)