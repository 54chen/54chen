title: riak源码阅读手记 运行安装
link: http://www.54chen.com/document/riak-source-install-run.html
author: 54chen
description: 
post_id: 1814
created: 2011/09/09 18:56:23
created_gmt: 2011/09/09 10:56:23
comment_status: open
post_name: riak-source-install-run
status: publish
post_type: post

# riak源码阅读手记 运行安装

![riak](http://wiki.basho.com/images/logo_wiki.png) riak源码阅读手记一 初出茅庐 项目入口 <http://www.54chen.com/_linux_/riak-source.html> riak源码阅读手记二 左右开弓 启动 <http://www.54chen.com/_linux_/riak-cource-code.html> **代码运行入口** riak-kv/ebin/riak_kv.app文件定义了otp标准项目的结构，一个不错的实例：http://www.iteye.com/topic/342819 {mod, {riak_kv_app, []}}一行定义了入口。打开riak_kv_app文件： -export([start/2,stop/1]). 定义了两大个方法，start和stop。 **先看start:** 启动riak_kv及相关的server。 检查系统时间。 加入系统变量。 确保设置NWR的值和一些vclock的初始值。 看storage_backend是否启动。 在cluster_info中注册。 启动supervisor。 全部启动。 stop：application:stop。 **跑一把试试：** A机：192.168.103.10 B机：192.168.97.48 C机：192.168.100.52 三个节点，已经装好erlang R13B04。 **先在A机操作**： 

> wget http://downloads.basho.com/riak/riak-0.14/riak-0.14.2.tar.gz tar zxvf riak-0.14.2.tar.gz cd riak-0.14.2 make rel

然后在rel/riak目录下会有最编译好的节点程序。 

> scp -r rel root@192.168.100.52:/root/ scp -r rel root@192.168.97.48:/root/

cd rel/riak ABC三机修改配置： vim etc/app.config 把127.0.0.1修改为对应的ip vim etc/vm.args 同上。 A：bin/riak start B: bin/riak start bin/riak-admin join riak@192.168.103.10 C: bin/riak start bin/riak-admin join riak@192.168.103.10 ./riak-admin status 欧了。 此时默认使用bitcask做为存储引擎。