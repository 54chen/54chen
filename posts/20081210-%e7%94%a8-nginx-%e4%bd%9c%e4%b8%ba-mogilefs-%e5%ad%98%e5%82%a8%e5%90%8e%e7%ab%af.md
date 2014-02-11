title: 用 Nginx 作为 MogileFS 存储后端 
link: http://www.54chen.com/life/%e7%94%a8-nginx-%e4%bd%9c%e4%b8%ba-mogilefs-%e5%ad%98%e5%82%a8%e5%90%8e%e7%ab%af.html
author: cc0cc
description: 
post_id: 320
created: 2008/12/10 13:26:56
created_gmt: 2008/12/10 05:26:56
comment_status: open
post_name: %e7%94%a8-nginx-%e4%bd%9c%e4%b8%ba-mogilefs-%e5%ad%98%e5%82%a8%e5%90%8e%e7%ab%af
status: private
post_type: post

# 用 Nginx 作为 MogileFS 存储后端 

1. 创建开发子目录 dev/，下载 Nginx 源代码并解压到其下，最新 Nginx 稳定版代码为 [nginx-0.5.35](http://sysoev.ru/nginx/nginx-0.5.35.tar.gz)： 
    
        $ mkdir dev
    $ cd dev
    $ wget http://sysoev.ru/nginx/nginx-0.5.35.tar.gz
    $ tar xzf nginx-0.5.35.tar.gz
    $ cd nginx-0.5.35

  2. 安装必要的开发包： 
    
        $ yinst i ports/pcre-devel

  3. 编译安装 Nginx，步骤同普通的开源软件，但需要增加选项编译 WebDAV[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/WebDAV?topicparent=PlatForm.%e7%94%a8Nginx%e4%bd%9c%e4%b8%baMogileFS%e5%ad%98%e5%82%a8%e5%90%8e%e7%ab%af) 支持模块： 
    
        $ ./configure --with-http_dav_module
    $ make
    $ sudo make install

安装完成后 Nginx 就被放到 /usr/local/nginx/ 目录下了，其中各个目录的功能为： 
    
        /usr/local/nginx/sbin/    存放 Nginx 主程序
    /usr/local/nginx/conf/   Nginx 配置文件目录
    /usr/local/nginx/html/    默认情况下 Nginx 的文档服务主目录
    /usr/local/nginx/logs/    Nginx 日志文件目录，内含 error.log、access.log 等日志文件
    /usr/local/nginx/client_body_temp/    外部服务处理程序响应结果暂存目录（不开放给普通用户访问）
    /usr/local/nginx/fastcgi_temp/    外部 FastCGI 服务程序响应结果暂存目录（不开放给普通用户访问）
    /usr/local/nginx/proxy_temp/     代理服务程序响应结果暂存目录（不开放给普通用户访问）

  4. 更改 Nginx 配置文件 `/usr/local/nginx/conf/nginx.conf` ，更新后的内容如下： 
    
        user nobody;
    worker_processes 5;
    events {
      worker_connections 1024;
    }
    http {
      include conf/mime.types;
      default_type application/octet-stream;
      sendfile on;
      keepalive_timeout 0;
      tcp_nodelay on;
      client_max_body_size **100M**;
      server {
          listen **7500**;
          server_name localhost;
          charset utf-8;
          location / {
              root **/home/y/var/mogdata/**;
              dav_methods put delete mkcol copy move;
              dav_access user:rw group:rw all:r;
          }
          error_page 500 502 503 504 /50x.html;
          location /50x.html {
              root html;
          }
      }
    }

其中 7500 是 [MogileFS](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/MogileFS) WebDAV[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/WebDAV?topicparent=PlatForm.%e7%94%a8Nginx%e4%bd%9c%e4%b8%baMogileFS%e5%ad%98%e5%82%a8%e5%90%8e%e7%ab%af) 服务的默认监听端口，存储根目录设在了 `/home/y/var/mogdata/` 目录下。
  5. 建立一个空文件 `/home/y/var/mogdata/index.html` ，此举是为了满足 [MogileFS](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/MogileFS) 检测后端 WebDAV[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/WebDAV?topicparent=PlatForm.%e7%94%a8Nginx%e4%bd%9c%e4%b8%baMogileFS%e5%ad%98%e5%82%a8%e5%90%8e%e7%ab%af) 服务是否存活而设。Nginx 默认带有目录 index 功能，只要在 `location /` 一节中加入 `autoindex on;` 命令即可，但此举有安全性问题，因此最好还是建一个空 index.html 文件了事。