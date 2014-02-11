title: 使用MySQL对lighttpd进行虚拟主机配置
link: http://www.54chen.com/life/%e4%bd%bf%e7%94%a8mysql%e5%af%b9lighttpd%e8%bf%9b%e8%a1%8c%e8%99%9a%e6%8b%9f%e4%b8%bb%e6%9c%ba%e9%85%8d%e7%bd%ae.html
author: cc0cc
description: 
post_id: 282
created: 2008/12/10 11:00:13
created_gmt: 2008/12/10 03:00:13
comment_status: open
post_name: %e4%bd%bf%e7%94%a8mysql%e5%af%b9lighttpd%e8%bf%9b%e8%a1%8c%e8%99%9a%e6%8b%9f%e4%b8%bb%e6%9c%ba%e9%85%8d%e7%bd%ae
status: private
post_type: post

# 使用MySQL对lighttpd进行虚拟主机配置

在安装lighttpd的时候要加上--with-mysql的参数。之后如下： _1\. 在lighttpd.conf中加入：_
    
    
      server.modules += ( "mod_mysql_vhost" )
      mysql-vhost.db = "es_dom"
      mysql-vhost.user = "es_auth"
      mysql-vhost.pass = ""
      mysql-vhost.sock = "/tmp/mysql.sock"
      mysql-vhost.sql = "SELECT webroot FROM es_domains WHERE domain='?';"

_2\. 在mysql中执行：_
    
    
      GRANT SELECT ON es_dom.* TO es_auth@localhost IDENTIFIED BY _;_
      CREATE DATABASE es_dom;
      USE es_dom;
      CREATE TABLE domains (
      domain varchar(64) not null primary key,
      webroot varchar(128) not null
      );
      INSERT INTO domains VALUES ('bbs.yisou.com','/home/es/share/bbs/htdocs');

_3\. 重启你的lighttpd_ ok了！ 

取自"<http://10.62.164.49/wiki/index.php/%E4%BD%BF%E7%94%A8MySQL%E5%AF%B9lighttpd%E8%BF%9B%E8%A1%8C%E8%99%9A%E6%8B%9F%E4%B8%BB%E6%9C%BA%E9%85%8D%E7%BD%AE>"