title: nginx防hashdos模块释出
link: http://www.54chen.com/web-ral/nginx-hashdos-module.html
author: 54chen
description: 
post_id: 1962
created: 2012/01/06 11:16:21
created_gmt: 2012/01/06 03:16:21
comment_status: open
post_name: nginx-hashdos-module
status: publish
post_type: post

# nginx防hashdos模块释出

![nginx hashdos](http://wiki.nginx.org/local/nginx-logo.png) 2012.1.7 更新  
编译的时候推荐使用nginx-1.0以上版本，不要加--with-debug参数编译，（感谢agentzh指出）。 hashdos这个事，严格意义上不是各种语言的错了（不过perl的确处理得很好），但是用nginx来擦屁股要干净些。 借鉴tomcat的作法，实现了下面这个nginx-http-hashdos-module，通过设置hashdos（默认on）的开关和body_max_count(默认值1000)，对nginx后面的服务进行安全防护，相比对php或者java进行patch，这或许是最好的办法了。 **nginx-http-hashdos-module项目地址** <https://github.com/54chen/nginx-http-hashdos-module> **如何使用** 1.下载zip后保存到一个目录，如~/nginx-http-hashdos-module。 2.cd nginx-1.0.9/ 3.重新编译和安装nginx ./configure --prefix=/opt/soft/nginx --with-pcre --user=www --group=www --with-http_stub_status_module --with-http_ssl_module --add-module=/path_to/nginx-http-hashdos-module/ && make && make install 4.配置打开： hashdos on; body_max_count 1000; location ~ \\.php$ { fastcgi_pass 127.0.0.1:9000; fastcgi_index index.php; fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; include /opt/soft/nginx/conf/fastcgi_params; client_body_buffer_size 2m; client_max_body_size 2m; } **注意事项** 因为计算参数都在内存中进行，所以client_body_buffer_size 与 client_max_body_size 的值一定要相等。推荐2m。 **TODO** 改进in-file时的post分析。 **原理** 介于微博上有网友对此文扫一眼之后以为是简单通过client_body_buffer_size和client_max_body_size来做的判断，特加此节。 nginx-reqeust-body在接到请求时，根据header中的声明，判断是保存在内存还是在硬盘中，当大小超过两个buf和client_body_buffer_size大小时，会写入临时文件。 防止hashdos的终极目标是filter用户的输入，所以对用户的输入参数数量进行计数。超过1000（body_max_count的默认数量）时，返回一个413给攻击者。希望不要再渔到各种高级工程师。 <https://github.com/54chen/nginx-http-hashdos-module>

## Comments

**[54chen](#14222 "2012-01-07 10:58:47"):** 昨天有个fixed，可能有checkin后你又下了吧。

**[54chen](#14223 "2012-01-07 10:59:10"):** java用的是hashmap而非array

**[yaodong](#14235 "2012-01-10 16:39:07"):** nginx/1.0.10 刚刚测试 body_max_count 这个参数依然不识别 看来想改数值 暂时只能修改这里了吧。 ngx_conf_merge_value(conf->body_max_count,prev->body_max_count,1000);

**[54chen](#14225 "2012-01-07 11:21:00"):** 结果：1.0.10的参数检查严格了，今天我fix一下，你可以先把body_max_count参数注释掉，默认是1000个。

**[yaodong](#14226 "2012-01-07 12:12:11"):** 谢谢 辛苦啦， 我会及时关注 如果有更新的话。

**[54chen](#14236 "2012-01-10 16:49:51"):** 您能提供下zip包的名字、configure参数、配置、出错提示吗？ 我这边nginx/1.0.10未重现。

**[54chen](#14224 "2012-01-07 10:59:43"):** 1.0.9下测试通过，今天我试试1.0.10版本

**[54chen](#14227 "2012-01-07 12:16:37"):** 感谢测试，已经fixed且push到git

**[yaodong](#14217 "2012-01-06 21:58:57"):** nginx: [emerg] unknown directive "body_max_count" in /opt/cafe/nginx/conf/nginx.conf:53 nginx: configuration file /opt/cafe/nginx/conf/nginx.conf test failed 按照要求编译后 不知道为什么 会出现上面的错误 /opt/cafe/nginx/sbin/nginx -V nginx: nginx version: nginx/1.0.10 nginx: TLS SNI support enabled nginx: configure arguments: --prefix=/opt/cafe/nginx-1.0.10 --with-http_realip_module --with-http_sub_module --with-http_flv_module --with-http_dav_module --with-http_gzip_static_module --with-http_stub_status_module --with-http_addition_module --with-pcre=/data/repos/pcre-8.00 --with-openssl=/data/repos/openssl-1.0.0e --with-http_ssl_module --with-zlib=/data/repos/zlib-1.2.3 --add-module=/data/repos/ngx_cache_purge-1.4 --add-module=/data/repos/nginx-http-hashdos-module

**[yaodong](#14218 "2012-01-06 22:08:49"):** [root@localhost repos]# md5sum 54*.* 6c47143bccdbd3f0552dec0e519f4f4f 54chen-nginx-http-hashdos-module-1c4f597 (1).zip 6c47143bccdbd3f0552dec0e519f4f4f 54chen-nginx-http-hashdos-module-1c4f597 (2).zip bfcaaf180f449530516afd22ec6d4864 54chen-nginx-http-hashdos-module-1c4f597.tar.gz 6c47143bccdbd3f0552dec0e519f4f4f 54chen-nginx-http-hashdos-module-1c4f597.zip [root@localhost repos]# cd 54chen-nginx-http-hashdos-module-1c4f597 貌似md5 不对啊

**[tanglei](#14220 "2012-01-07 10:12:04"):** 求解Java中的hash原理。有疑问啊http://www.tanglei.name/use-hash-collision-to-realize-dos-in-php-and-java/

