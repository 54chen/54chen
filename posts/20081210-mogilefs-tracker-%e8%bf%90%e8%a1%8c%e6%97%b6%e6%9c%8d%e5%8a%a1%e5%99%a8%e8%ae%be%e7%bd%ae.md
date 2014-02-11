title: MogileFS Tracker 运行时服务器设置 
link: http://www.54chen.com/life/mogilefs-tracker-%e8%bf%90%e8%a1%8c%e6%97%b6%e6%9c%8d%e5%8a%a1%e5%99%a8%e8%ae%be%e7%bd%ae.html
author: cc0cc
description: 
post_id: 324
created: 2008/12/10 13:40:40
created_gmt: 2008/12/10 05:40:40
comment_status: open
post_name: mogilefs-tracker-%e8%bf%90%e8%a1%8c%e6%97%b6%e6%9c%8d%e5%8a%a1%e5%99%a8%e8%ae%be%e7%bd%ae
status: private
post_type: post

# MogileFS Tracker 运行时服务器设置 

[MogileFS](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/MogileFS) 的元数据 DB 中会保存一些服务器设置选项，用以在系统运行时微调 Tracker 服务的行为，下面是对常用服务器设置的总结列表。 

**设置参数** **含义** **取值范围** **例子**

_enable_rebalance_
决定是否开启 Tracker 的自动存储节点平衡功能，开启该功能会使 Tracker 在空闲时自动根据各个存储节点的使用率调整文件的分布，尽量使不同存储节点的磁盘使用率相近。
1/t/on/y 开头表示开启，0/f/off/n 开头表示关闭
`enable_rebalance=1`

_memcache_servers_
列出 Tracker 可以使用的 memcached 服务器地址，Tracker 可以用 memcache 来缓存文件位置信息，加快响应客户端请求的速度。(Tracker 所在节点上需要安装 `Cache::Memcached` 模块)
以逗号分隔的 memcached 服务器地址列表
`memcache_servers=10.62.162.75,10.62.162.119:11211`

_network_zones_
指定子网名称列表，作为 MultipleNetwork[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/MultipleNetwork?topicparent=PlatForm.MogileFSTrackerSettings) 冗余策略的目标网络候选项。各个子网的具体 IP 区段在 `zone_子网名称` 设置中指定
以逗号分隔的子网名称字符串
`network_zones=beijing, hangzhou` `zone_beijing=123.118.0.0/16` `zone_hangzhou=121.0.16.0/24`

_rebalance_policy_
设定自动平衡策略模块，不同的自动平衡策略会影响最终平衡的结果。默认策略为 `MogileFS::RebalancePolicy::PercentFree` (由 `MogileFS::Worker::Replicate::rebalance_policy_obj()` 方法确定)。
目前可用的策略为 `DrainDevices` 、 `PercentFree` 、 `Random`
`rebalance_policy=MogileFS::RebalancePolicy::Random`

_schema_version_
[MogileFS](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/view/PlatForm/MogileFS) 数据库方案版本号
1.0
`schema_version=1.0`

_no_schema_check_
决定是否不进行数据库方案版本号验证
1 表示不进行验证，0 表示进行验证
`no_schema_check=0`

_slave_keys_
元数据库的所有slave节点名称列表。各个slave节点的具体连接设置在 `slave_节点名称` 设置中指定
以逗号分隔的slave节点名称字符串
`slave_keys=a,b` `slave_a=DBI:mysql:mogilefs:10.62.162.75|mogilefs|mogilefs` `slave_b=DBI:mysql:mogilefs:10.62.162.120|mogilefs|mogilefs`