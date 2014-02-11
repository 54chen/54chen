title: [警示]Nginx + PHP CGI的安全漏洞：fix_pathinfo
link: http://www.54chen.com/php-tech/nginx-php-cgi-of-security-hole.html
author: 54chen
description: 
post_id: 1161
created: 2010/05/20 19:09:14
created_gmt: 2010/05/20 11:09:14
comment_status: open
post_name: nginx-php-cgi-of-security-hole
status: publish
post_type: post

# [警示]Nginx + PHP CGI的安全漏洞：fix_pathinfo

![php漏洞](http://img05.taobaocdn.com/bao/uploaded/i5/T1.RRAXjVDXXX7OsE8_070026.jpg) 如果你正在使用nginx+php，请关注。 **表像：** 具体的重现过程，用php代码修改后缀名后上传，比如说www.xxx.com/1.jpg，访问的时候用www.xxx.com/1.jpg/xxx.php 

> **这段jpg代码将会被执行！！！**

<http://docs.php.net/manual/zh/ini.core.php> cgi.fix_pathinfo "1" PHP_INI_ALL 从 PHP 4.3.0 起可用 **请注意：默认为1** **解决办法：** 1.修改php.ini中的cgi.cgi.fix_pathinfo为0（即使你在php.ini中没有搜到，也要设置，没有搜到表示默认为1） 2.判断文件上传类型时使用严格的判断，至于怎么判断，参见：<http://www.54chen.com/php-tech/php-upload-file-types-to-determine-the-complete-program-and-php-nginx-upload-size-and-complete-control-program.html> 3.把nginx的判断正则修改为去除/ if ( $fastcgi_script_name ~ \\..*\/.*php ) { return 403; } 鸟哥在<http://www.laruence.com/2010/05/20/1495.html>一文中提及此事。

## Comments

**[aa](#12368 "2010-05-21 17:06:48"):** 你这个没有上传的时候就不能让他上传

