title: ubuntu下Empathy十月起无法登录msn的解决办法
link: http://www.54chen.com/_linux_/ubuntu-empathy-msn-bug.html
author: 54chen
description: 
post_id: 1460
created: 2010/11/15 20:06:50
created_gmt: 2010/11/15 12:06:50
comment_status: open
post_name: ubuntu-empathy-msn-bug
status: publish
post_type: post

# ubuntu下Empathy十月起无法登录msn的解决办法

![ubuntu](http://img03.taobaocdn.com/imgextra/i3/T1BdNRXgNGXXbbb_g__080543.jpg) 作为ubuntu深度用户，我（54chen）在几个星期前就有网友来问过，msn登录常常会挂掉，甚至到现在已经无法再登录，受影响的范围很广，最新的10.10也是一样的，原因可能是msn单方面更新了接口，下面提供一种解决办法（以10.04lts Empathy 2.30.1.1 为例）： #vim /usr/share/pyshared/papyon/service/description/SingleSignOn/RequestMultipleSecurityTokens.py 找到第24行注释掉： #CONTACTS = ("contacts.msn.com", "?fs=1&id;=24000&kv;=7&rn;=93S9SWWw&tw;=0&ver;=2.1.6000.1") 修改为： CONTACTS = ("contacts.msn.com", "MBI") （请勿复制，引号有转义） 然后重启，恭喜你msn高昂登录。 附： [ubuntu下empathy的msn群显示昵称](/_linux_/ubuntu-1004-lts-version-of-the-empathy-msn-group-chat-under-the-nickname-display-method.html) 点击下载我修改过的文件： [ http://www.54chen.com/RequestMultipleSecurityTokens.py.gz ]( http://www.54chen.com/RequestMultipleSecurityTokens.py.gz )

> gunzip RequestMultipleSecurityTokens.py.gz sudo cp RequestMultipleSecurityTokens.py /usr/share/pyshared/papyon/service/description/SingleSignOn/

## Comments

**[openepo](#13348 "2011-01-27 15:34:45"):** really works for me. Ubuntu 10.10 DT i386.

