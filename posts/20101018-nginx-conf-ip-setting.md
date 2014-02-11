title: nginx.conf控制指定的代理ip和ip访问的设置手记
link: http://www.54chen.com/_linux_/nginx-conf-ip-setting.html
author: 54chen
description: 
post_id: 1391
created: 2010/10/18 09:35:23
created_gmt: 2010/10/18 01:35:23
comment_status: open
post_name: nginx-conf-ip-setting
status: publish
post_type: post

# nginx.conf控制指定的代理ip和ip访问的设置手记

![nginx conf](http://img01.taobaocdn.com/imgextra/i1/T1nZJOXfFJXXcYn1U9_103242.jpg) 工作中有一次用到利用nginx的配置来让只有公司ip的访问才能打开指定的后台url，于是有了下面的记录。 在nginx中if很弱，http://www.nginxcn.com/doc/standard/httprewrite.html，基本上不能写太复杂的条件或者是嵌套。 因为公司我([54chen]())网络的设置，过去打到服务器的ip有可能是几个ip，同时也有可能是代理的ip，所以在if判断的时候，可能有多个条件。 

> location /administrator { #log_format www_54chen_com '$remote_addr - $remote_user [$time_local] $request ' # '"$status" $body_bytes_sent "$http_referer" ' # '"$http_user_agent" "$http_x_forwarded_for"'; # access_log /data/www.log www_54chen_com; set $fuck 0; if ($remote_addr = '1.1.1.1'){ set $fuck 1; } if ($remote_addr = '1.1.1.2'){ set $fuck 1; } if ($remote_addr = '1.1.1.3'){ set $fuck 1; } if ($http_x_forwarded_for = '2.2.2.2') { set $fuck 1; } if ($fuck = 0){ return 404; } #此处还需要填写和其他location一样的以提供正常服务环境 } 

1） 1.1.1.1 1.1.1.2 1.1.1.3都是直接ip地址 2) 2.2.2.2是代理之前的ip地址 另外流行的另一种做法： 

> allow 1.1.1.1; allow 1.1.1.2; allow 1.1.1.3; deny all;

但此方法我([54chen]())始终没找到支持代理的判断。

## Comments

**[agentzh](#13112 "2010-10-18 13:24:13"):** 呵呵，可以考虑使用 ngx_lua 模块的 set_by_lua 来在 rewrite 阶段作复杂的条件判断逻辑。 详情见 http://github.com/chaoslawful/lua-nginx-module 内嵌这点 Lua 对整体性能的影响是非常小的 :)

**[54chen](#13113 "2010-10-18 13:43:34"):** 用这种方法把需求搞定了，中间差点没搞定的时候就想到了哥的ngx_lua，哈哈。:)

