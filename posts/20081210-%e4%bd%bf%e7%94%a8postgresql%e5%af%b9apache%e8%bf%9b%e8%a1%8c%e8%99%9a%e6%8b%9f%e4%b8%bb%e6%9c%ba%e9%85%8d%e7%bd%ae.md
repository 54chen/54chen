title: 使用PostgreSQL对apache进行虚拟主机配置
link: http://www.54chen.com/life/%e4%bd%bf%e7%94%a8postgresql%e5%af%b9apache%e8%bf%9b%e8%a1%8c%e8%99%9a%e6%8b%9f%e4%b8%bb%e6%9c%ba%e9%85%8d%e7%bd%ae.html
author: cc0cc
description: 
post_id: 284
created: 2008/12/10 11:00:36
created_gmt: 2008/12/10 03:00:36
comment_status: open
post_name: %e4%bd%bf%e7%94%a8postgresql%e5%af%b9apache%e8%bf%9b%e8%a1%8c%e8%99%9a%e6%8b%9f%e4%b8%bb%e6%9c%ba%e9%85%8d%e7%bd%ae
status: private
post_type: post

# 使用PostgreSQL对apache进行虚拟主机配置

## 使用PostgreSQL对apache进行虚拟主机配置

准备工作 

  * Apache需要支持rewrite这个模块
  * 要有PostgreSQL的client的lib
  * 和DBD::PG相关的perl包
apache配置文件中加入： 
    
    
      RewriteEngine on
      RewriteLog  /home/es/apache2/logs/apache2-rewrite.log
      RewriteLogLevel 9
      RewriteLock /home/nmail/tmp/apache2-rewrite.lock
      RewriteMap  lowercase  int:tolower
      RewriteMap  vhostuser  prg:/home/es/apache/conf/vhostuser.pl
      RewriteCond  %{IS_SUBREQ}		^true 
      RewriteCond  %{HTTP_HOST}               ^(.+)$
      RewriteCond  ${vhostuser:%1}            ^(.+)$
      RewriteRule  ^/(.*)$                    /~%1/html/$1 [L]
      RewriteCond  %{REQUEST_URI}             ^/cgi-bin/
      RewriteCond  %{HTTP_HOST}	        ^(.+)$
      RewriteCond  ${vhostuser:%1}            ^(.+)$
      RewriteRule  ^/cgi-bin/(.*)$            /~%1/cgi-bin/$1 [PT]