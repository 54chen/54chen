title: 如何让squid 2.6.STABLE21输出Content-Encoding: gzip
link: http://www.54chen.com/_linux_/how-to-make-squid-26stable21-output-content-encoding-gzip.html
author: cc0cc
description: 
post_id: 995
created: 2010/03/08 17:25:41
created_gmt: 2010/03/08 09:25:41
comment_status: open
post_name: how-to-make-squid-26stable21-output-content-encoding-gzip
status: publish
post_type: post

# 如何让squid 2.6.STABLE21输出Content-Encoding: gzip

**问题描述：** 一个WEB服务器，跑[nginx ](/architecture/wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes.html)0.6。前端一squid 2.6代理。用户访问先经过squid。 现需要squid服务器输出gzip压缩。 **问题解决方案：** 原来的nginx已经支持[gzip](http://www.54chen.com/_linux_/linux-system-replacement-method-sshd-notes-from-from-academy-of-sciences.html)，修改或者增加nginx.conf中内容（打开nginx的gzip支持附后）： gzip_http_version 1.0; gzip_vary on; add_header Vary Accept-Encoding; 清空原来的squid对应的缓存，新缓存即是带了gzip的header。 **参考文献：** <http://wiki.nginx.org/NginxHttpGzipModule> **附录：** [如何打开gzip，【优化php\nginx】 一段。](http://www.54chen.com/architecture/wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes.html)