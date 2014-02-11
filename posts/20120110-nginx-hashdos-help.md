title: nginx防hashdos模块使用帮助
link: http://www.54chen.com/web-ral/nginx-hashdos-help.html
author: 54chen
description: 
post_id: 1976
created: 2012/01/10 11:48:30
created_gmt: 2012/01/10 03:48:30
comment_status: open
post_name: nginx-hashdos-help
status: publish
post_type: post

# nginx防hashdos模块使用帮助

![nginx,hashdos](http://wiki.nginx.org/local/nginx-logo.png) 经过上周一周朋友们帮忙测试和bug fix，nginx_http_hashdos_module已经达到可以线上使用的水平，下面是使用记录。 **下载**

> #wget --no-check-certificate https://github.com/54chen/nginx-http-hashdos-module/zipball/master #mv master nginx_hashdos.zip #unzip nginx_hashdos.zip

**编译安装**

> #tar zxvf nginx-1.0.xx.tar.gz #cd nginx-1.0.xx/ #./configure --prefix=/opt/soft/nginx --with-pcre --user=www --group=www --with-http_stub_status_module --with-http_ssl_module --add-module=../54chen-nginx-http-hashdos-module-f84d909 #make && make install

**配置注意事项** 在http段，增加如下： 

> hashdos on; body_max_count 1000; 

在各自的location段，要按照业务情况来加： 

> client_body_buffer_size 2m; client_max_body_size 2m;

*上述两个值一定要相等。 如果是普通的discuz，上传上限是1m的，可以修改为1m。 如果是没有上传功能的普通网站，建议修改为512k。