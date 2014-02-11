title: ubuntu 10.04 LTS版本下的Empathy MSN群聊显示昵称方法
link: http://www.54chen.com/_linux_/ubuntu-1004-lts-version-of-the-empathy-msn-group-chat-under-the-nickname-display-method.html
author: 54chen
description: 
post_id: 1191
created: 2010/06/04 19:23:44
created_gmt: 2010/06/04 11:23:44
comment_status: open
post_name: ubuntu-1004-lts-version-of-the-empathy-msn-group-chat-under-the-nickname-display-method
status: publish
post_type: post

# ubuntu 10.04 LTS版本下的Empathy MSN群聊显示昵称方法

![54chen ubuntu empathy msn](http://img06.taobaocdn.com/bao/uploaded/i6/T1BulCXhXkXXXYjVM9_072304.jpg)

## **1.关系普及**

Empathy是个托，python-papyon是个python实现的msn库，telepathy-butterfly是个完成msn功能的python客户端。 

## **2.修改办法**

sudo vim /usr/share/pyshared/papyon/conversation.py 查找 if message_type == 这个字符串 找到内容为： 

> if message_type == 'text/plain': msg = ConversationMessage(unicode(message.body, message_encoding), TextFormat.parse(message_formatting), self.__last_received_msn_objects) try: display_name = message.get_header('P4-Context')

将if判断后try之前中间定义msg这一堆内容修改为如下： 

> try: msg = ConversationMessage(unicode("["+message.get_header('P4-Context')+"]"+message.body, message_encoding), TextFormat.parse(message_formatting), self.__last_received_msn_objects) except KeyError: msg = ConversationMessage(unicode(message.body, message_encoding), TextFormat.parse(message_formatting), self.__last_received_msn_objects)

保存后重新启动empathy，msn群里就能显示昵称鸟。 ![empathy](http://img02.taobaocdn.com/bao/uploaded/i2/T1_udCXfXjXXbldwg4_053033.jpg) [点击此处下载我的conversation.py文件](/conversation.py.gz) 下载下来后执行： 

> chen@54chen:~$ gunzip conversation.py.gz chen@54chen:~$ sudo cp conversation.py /usr/share/pyshared/papyon/

**我的版本是：Empathy 2.30.1**

## Comments

**[54chen](#12421 "2010-06-08 08:56:40"):** 兄弟最好替换后重启一下系统，因为butterfly在重新启动msn时需要一段时间才能真正重启，网络错误是butterfly进程还在。 reboot或者是killall telepathy-butterfly 都可以。

**[54chen](#12423 "2010-06-08 14:56:40"):** 有不少朋友在修改python的时候遇到了问题，特上传了我的修改过的文件，可直接复盖使用

**[摇光](#12420 "2010-06-08 08:36:34"):** 你的表述不太清楚么。。替换后显示网络错误了

**[guyu](#12431 "2010-06-11 22:37:04"):** 我被插图震惊了

**[Chandler](#13012 "2010-08-27 12:40:37"):** 网络错误是因为作者给的引号有问题，你把“替换对了就可以用了

**[丽水生活网](#12469 "2010-06-28 12:53:53"):** 大牛你也用ubuntu啊？感觉还是ubuntu方便，俺现在服务器也用ubuntu的~

