title: mediawiki脚本自动发内容实现
link: http://www.54chen.com/php-tech/mediawiki-scripts-auto-post.html
author: 54chen
description: 
post_id: 1755
created: 2011/07/01 10:29:07
created_gmt: 2011/07/01 02:29:07
comment_status: open
post_name: mediawiki-scripts-auto-post
status: publish
post_type: post

# mediawiki脚本自动发内容实现

![mediawiki auto post](http://img04.taobaocdn.com/imgextra/i4/T1r11iXhVkXXX_.NQ8_100702.jpg) 相信很多公司都是在使用mediawiki做内部team的知识管理，同时也有一些边边角角的脚本去做诸如上线、报警一类的工作。经常会有这样的需求：在上线的时候自动更新wiki的一页内容。 实现起来很简单，只需要一个curl即可，mediawiki提供了http restful的接口，在api.php上，可以做很多事情，下面是实际使用的举例。 目标：上线脚本最后执行一下记录，更新wiki里的一页做为上线记录，Online logging包换的元素有：日期，项目，版本号，机器。 环境：安装了 curl的机器 **实现细节（wiki.sh）：**

> #! /bin/sh OPS_APP=$1 shift OPS_REV=$1 shift OPS_SERVER=$1 OPS_TIME=`date` echo "\nwrite to wiki" TOKEN=`curl -s -d 'action=login&lgname;=你的用户名&lgpassword;=你的密码&format;=json' http://你的wiki域名/wiki/api.php -D cookies.txt -b cookies.txt | sed -e 's/.*token":"//' -e 's/".*//'` curl -s -d "action=login&lgname;=你的用户名&lgpassword;=你的密码&lgtoken;=${TOKEN}&format;=json" http://你的wiki域名/wiki/api.php -b cookies.txt -D cookies.txt > result RET=`curl -s 'http://你的wiki域名/wiki/api.php?action=query∝=info&intoken;=edit&titles;=Online_logging&indexpageids;=&format;=json' -b cookies.txt -D cookies.txt ` ETOKEN=`echo ${RET}|sed -e 's/.*token":"//' -e 's/".*//' -e 's/+/%2B/g'` ETOKEN=`echo ${ETOKEN}|sed -e 's/\\\\\\\/%5C/g'` TIME=`echo ${RET}|sed -e 's/.*starttimestamp":"//' -e 's/".*//'` URL="action=edit&title;=Online_logging&section;=0&appendtext;=%0A|${OPS_TIME}||${OPS_APP}||${OPS_REV}||${OPS_SERVER} %0A |-&summary;=robot change&starttimestamp;=${TIME}&token;=${ETOKEN}&format;=json" curl -s -d "${URL}" http://你的wiki域名/wiki/api.php -b cookies.txt -D cookies.txt > result echo "\n**write successful**\n" 

**具体使用：** 首先新建一个名字为Online_logging的页面，内容如下： 

> {|border="1" cellspacing="0" |- ! 日期 || 项目 || 版本号 || 机器 |-

> sh wiki.sh 项目 版本号 服务器ip

**api参考：** http://www.mediawiki.org/wiki/API:Login

## Comments

**[Anthony](#13643 "2011-07-05 12:50:40"):** 感谢分享，以前用过mediawiki一段时间，感觉企业应用不如confluence好，后来就改用confluence了。

