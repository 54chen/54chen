title: APACHE常用设置
link: http://www.54chen.com/php-tech/apache-common-set.html
author: admin
description: 
post_id: 26
created: 2006/10/23 22:49:29
created_gmt: 2006/10/23 14:49:29
comment_status: open
post_name: apache-common-set
status: publish
post_type: post

# APACHE常用设置

KeepAliveTimeout 5 是允许用户保持连接5秒内有效，如果用户在5秒内点击其他页面不需要重新建立联结，这个值设置短不利于降低效率，如果设置长可能导致的同时联结数会跟高，一般建议按照缺省的15 MaxKeepAliveRequests 这个缺省是100 你这个设置太高了，这个使用户连续访问了多少个页面后重新建立联结，你这个值3000，好像太高了点 MinSpareServers 5 MaxSpareServers 10 StartServers 5 如果服务器经常有突发大量请求的话，建议值都稍微太高点 MaxClients 150 这个是服务器最大联结数，如果超过150联结的话系统就有等待情况，如果繁忙的话150是不够的，你可以用命令 ps -ef|grep httpd|wc -l 看看有多少联结，一直接近150的话，建议增大一些 MaxRequestsPerChild 0 这个建立设置10000，apache避免程序有异常，定义一个子进程完成多少个请求以后退出，如果是0就是永远不退出。如果对自己程序比较难把我的话建议设置一个稍大的值而不是0