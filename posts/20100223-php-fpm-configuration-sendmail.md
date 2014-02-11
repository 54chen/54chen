title: PHP-FPM配置sendmail支持PHP发信
link: http://www.54chen.com/php-tech/php-fpm-configuration-sendmail.html
author: cc0cc
description: 
post_id: 968
created: 2010/02/23 15:32:38
created_gmt: 2010/02/23 07:32:38
comment_status: open
post_name: php-fpm-configuration-sendmail
status: publish
post_type: post

# PHP-FPM配置sendmail支持PHP发信

当下常用的环境CentOS+nginx+php-fpm+mysql. 之前五四陈科学院曾有一文章是介绍[环境](/uncategorized/gae-java%e6%94%af%e6%8c%81%e8%af%95%e7%94%a8%e6%89%8b%e8%ae%b0.html)的配置的，挖一挖水，链接在这里： [wordpress nginx php-cgi mysql在128m内存下的vps安装和优化手记[原创]](http://www.54chen.com/architecture/wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes.html) 如果要使用[mail](http://www.54chen.com/flex/thousand-oaks-school-network-all-network-urgent-flash-as3-engineers-social-game-game-planning.html)功能，需要按如下步骤操作： 第一步、安装sendmail yum install sendmail 第二步、启动sendmail service sendmail start 第三步、设置为auto start chkconfig sendmail on 第四步、修改php-fpm.conf: 搜索sendmail，去掉那一行两头的注释如下： 

> <value name="sendmail_path">/usr/sbin/sendmail -t -i</value>

第五步、php-fpm reload