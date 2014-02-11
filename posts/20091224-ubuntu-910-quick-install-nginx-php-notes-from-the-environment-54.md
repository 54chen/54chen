title: ubuntu 9.10快速安装nginx+php环境手记[54chen科学院]
link: http://www.54chen.com/php-tech/ubuntu-910-quick-install-nginx-php-notes-from-the-environment-54.html
author: cc0cc
description: 
post_id: 905
created: 2009/12/24 17:17:05
created_gmt: 2009/12/24 09:17:05
comment_status: open
post_name: ubuntu-910-quick-install-nginx-php-notes-from-the-environment-54
status: publish
post_type: post

# ubuntu 9.10快速安装nginx+php环境手记[54chen科学院]

我([54chen]())因为场景需要，特地在[ubuntu9.10](http://www.54chen.com/_linux_/11-return-ubuntu-910-notes-from-the-hard-disk-installation.html)中安装nginx+php的环境，要快速不要优化，记录如下： 

> sudo apt-get install php5 php5-cgi php5-cli php5-gd php5-curl php5-mysql nginx sudo apt-get install lighttpd spawn-fcgi -a 127.0.0.1 -p 9000 -C 5 -u www-data -g www-data -f /usr/bin/php-cgi

上面的操作解读： 1.安装php nginx 2.安装lighttpd，为啥要安装这个，因为他有个spawn-fcgi，因为apt下来的php不是php-fpm。 3.用spawn-fcgi启动php-cgi在9000端口监听。 

> sudo vim /etc/php5/cgi/php.ini 修改php.ini文件 cgi.fix_pathinfo=1; 这样php-cgi方能正常使用SCRIPT_FILENAME这个变量

sudo vim /etc/nginx/sites-enabled/www.54chen.com 

> server { listen 80; //端口 server_name wiki.54chen.com; //虚拟域名 access_log /var/log/nginx/hwiki.54chen.com.access.log; //访问日志 location / { root /home/wiki.54chen.com; //size根目录 index index.php; } location ~ \\.php$ { #php fastcgi的配置 fastcgi_pass 127.0.0.1:9000; #php fastcgi的代理端口与ip fastcgi_index index.php; fastcgi_param SCRIPT_FILENAME /home/wiki.54chen.com$fastcgi_script_name; #要处理的php文件的路径 include /etc/nginx/fastcgi_params; #fastcgi的参数文件地址 } }

好，上面的操作就建立了一个[虚拟主机](/memo/%e4%b8%80%e5%8f%a5%e8%af%9d%e8%ae%b0%e5%bd%95%e5%a4%87%e5%bf%98.html)，保存好。 sudo killall apache2 杀掉apache，启动nginx. sudo /etc/init.d/nginx start 大功告成！整个过程不超过十五分钟。呼呼～～ 圣诞快乐！！！

## Comments

**[阿罗](#12130 "2010-01-21 00:01:03"):** 可以直接安装spawn,不需要lighttpd的。

