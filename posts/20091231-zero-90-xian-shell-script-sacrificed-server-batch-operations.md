title: ［零九零献］献出服务器批量操作shell脚本
link: http://www.54chen.com/_linux_/zero-90-xian-shell-script-sacrificed-server-batch-operations.html
author: cc0cc
description: 
post_id: 912
created: 2009/12/31 17:11:28
created_gmt: 2009/12/31 09:11:28
comment_status: open
post_name: zero-90-xian-shell-script-sacrificed-server-batch-operations
status: publish
post_type: post

# ［零九零献］献出服务器批量操作shell脚本

现在是北京时间零九年最后一天的下午五点 值此年末岁初的佳节时期 54chen祝订阅没订阅科学院日志的朋友们 元旦快乐！ 特地送出[shell](/_linux_/linux-system-replacement-method-sshd-notes-from-from-academy-of-sciences.html)脚本一个，适合用于经常性的和十台以上服务器打交道的同志们，解放双手。看码: 第一个文件 run.sh 功能 总控 运行的文件 是操作时的入口 #!/bin/sh SERVER_FILE=server SERVER_LIST="`cat ${SERVER_FILE}|grep -v "#"`" for SERVER in `echo "${SERVER_LIST}"` do echo ${SERVER} sh runOne.sh ${SERVER}; done; 第二个文件 runOne.sh 功能 自定义每个服务器要进行的操作的步骤 #!/bin/sh host=$1 ssh root@$host "yum -y install mysql-server" #上传 scp my.cnf root@$host:/etc/my.cnf #操作 第三个文件 server 功能 记录有哪些服务器要操作 ＃号可以[注释](http://www.54chen.com/_linux_/intelligent-dns-configuration-process.html)不想操作的机器 #10.22.206.21 10.22.206.22 10.22.206.23 10.22.206.24 10.22.206.25 使用的时候，先设置[server](http://www.54chen.com/memo/%e7%94%a8ssh-tunnel%e4%b8%b4%e6%97%b6%e7%aa%81%e7%a0%b4%e8%b7%a8%e6%9c%ba%e6%88%bf%e7%9a%84%e9%99%90%e5%88%b6.html)文件，直接./run.sh 就开始执行，灵活鸡动，方便快捷。

## Comments

**[阿罗](#12129 "2010-01-20 23:59:16"):** 你也开始玩shell了啊

