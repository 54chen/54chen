title: 分享一个批量扫描ip地址的shell内涵脚本
link: http://www.54chen.com/_linux_/sharing-a-shell-script.html
author: 54chen
description: 
post_id: 1177
created: 2010/05/27 21:35:21
created_gmt: 2010/05/27 13:35:21
comment_status: open
post_name: sharing-a-shell-script
status: publish
post_type: post

# 分享一个批量扫描ip地址的shell内涵脚本

![内涵人类](http://img01.taobaocdn.com/bao/uploaded/i1/T1IOXBXe8iXXbdtJjb_093433.jpg)

## **内涵前言**

假如您在维护上千台服务器，然后通过www.xxx.com发现打不开，然后有部分用户又说是可以打开的，现在要做的事情是找出来哪个机器可以用。 

## **内涵第一章 检查原理**

有许多DNS反查的网站，可以反查你的域名的相关ip地址，然后把这些ip地址循环ping，有返回结果的说明那个服务器可用。比如说just-dnslookup等等网站。具体可以在网上找找。 

## **内涵第二章 检查操作**

在准备好数据后，将结果复制到脚本文件中的temp文件中，然后修改54chenPing.sh中的第二行，将awk中的$3修改为你的数据对应的列数。比如说你复制的内容的第三列的话就写$3，第四列就写$4。 

## **内涵第三章 执行**

#sh 54chenPing.sh 然后会有结果输出，持续输出返回结果的一个或者多个ip即是可用的服务器。 

## **内涵第四章 代码**

> #!/bin/sh cat temp | awk '{print $3}' > ip.txt FLIST="`cat ip.txt|grep -v "#"`" for FILE in `echo "${FLIST}"` do ping ${FILE}> tmp/${FILE}.txt& done tail -f tmp/*

## **内涵第五章 下载**

[点击下载>>>](/54chenPing.tar.gz)

## **内涵第六章 备注**

要经常更新temp文件，参见第一章。要知道哪台服务器是可用的，参见第三章。 

## **内涵作者**

54chen(陈臻)，人人网分布式存储研究人员，业余时间混迹于各技术组织且乐此不疲。目前关注实施PHP培训。对flex等前端技术有一点研究。个人技术站点:http://www.54chen.com/ 。可以通过电子邮件 czhttp@gmail.com 联系到他。