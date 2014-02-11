title: redmine极速安装手记
link: http://www.54chen.com/web-ral/redmine-install-fast-detail.html
author: 54chen
description: 
post_id: 1784
created: 2011/08/03 08:40:06
created_gmt: 2011/08/03 00:40:06
comment_status: open
post_name: redmine-install-fast-detail
status: publish
post_type: post

# redmine极速安装手记

![redmine install ubuntu](http://img04.taobaocdn.com/imgextra/i4/T1RF46XddFXXXANWHX_084227.jpg) Redmine是用Ruby开发的基于基于web的项目管理软件，是用ROR框架开发的一套跨平台项目管理系统，系统通过“项目（Project）”的形式把成员、任务（问题）、文档、讨论以及各种形式的资源组织在一起，大家参与更新任务、文档等内容来推动项目的进度，同时系统利用时间线索和各种动态的报表形式来自动给成员汇报项目进度。 机器环境： 

> ubuntu 10.04 dell optiplex 380 root权限下操作

1）apt-get install mysql-client mysqld-server 2)进mysql: 

> create database redmine_default; GRANT all ON redmine_default.* TO root@'%' IDENTIFIED BY 'miliao'; FLUSH PRIVILEGES ;

3)apt-get install redmine redmine-mysql subversion libapache2-mod-passenger 4)ln -s /usr/share/redmine/public /var/www/redmine 5)a2enmod passenger 6)chmod a+x /usr/share/redmine/public 7)/etc/apache2/sites-available/default 增加： 

> <Directory /var/www/redmine> RailsBaseURI /redmine PassengerResolveSymlinksInDocumentRoot on </Directory>

8)/etc/init.d/apache2 restart 9)打开 http://xxx/redmine 排查问题： 1） /var/log/redmine下有500的原因 2）中文乱码问题： 

> 上述第8步时，不要着急做配置。把redmine_default整个数据库导出来，替换其中的charset=laitin1为charset=utf8，清空原表重新导入后再用admin/admin登录重新配置。

## Comments

**[chen1706](#13720 "2011-08-05 10:02:52"):** 一直作为项目管理工具，用了差不多有一年了 而且可以把svn 的信息绑定到一起 直观的查看上次提交做了哪些改动

