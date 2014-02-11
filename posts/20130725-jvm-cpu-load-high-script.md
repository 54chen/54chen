title: 线上jvm进程CPU load高排查脚本-jkiller
link: http://www.54chen.com/java-ee/jvm-cpu-load-high-script.html
author: 54chen
description: 
post_id: 2385
created: 2013/07/25 21:22:59
created_gmt: 2013/07/25 13:22:59
comment_status: open
post_name: jvm-cpu-load-high-script
status: publish
post_type: post

# 线上jvm进程CPU load高排查脚本-jkiller

![java](http://www.oracle.com/technetwork/java/javaspotlight-189455.png) **jkiller** 如果遇到线上java进程占用过多的cpu，可以用这个脚本来帮助你快速找到代码的问题。 <https://github.com/54chen/jkiller> **使用过程** 先用top或者是jps定位占用cpu过多的java进程的pid是多少。 然后执行如下过程即可得到结论： 

> #wget https://raw.github.com/54chen/jkiller/master/jkiller.sh && chmod +x jkiller.sh #sh jkiller.sh [pid] 

*centos系统下测试通过。