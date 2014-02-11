title: 淘宝分布式数据库系统OceanBase观后闲话小记
link: http://www.54chen.com/architecture/taobao-oceanbase-intro.html
author: 54chen
description: 
post_id: 1807
created: 2011/09/01 10:11:06
created_gmt: 2011/09/01 02:11:06
comment_status: open
post_name: taobao-oceanbase-intro
status: publish
post_type: post

# 淘宝分布式数据库系统OceanBase观后闲话小记

http://code.taobao.org/trac/OceanBase/wiki/intro 讲得还比较细。 **数据模型：** oceanBase采用类似cassandra的tablet/sstable数据模型实现，简单说就是bigtable的数据模型。 **系统目标：** 其目标比较明确，是一个强CA类系统，P（分区能力）。目标是强一致性、高可用性，分区能力稍弱。从文档上看，他们并不担心的快速的数据增长量。 **去中心化：** 见图： ![taobao oceanbase](http://code.taobao.org/trac/OceanBase/raw-attachment/wiki/intro/test.JPG) RootServer/UpdateServer是一个中心节点，这个有死翘翘的可能性（好吧，他们搞了一主一备）。 **支持的操作：** 基础的merge/join都可以，一般使用上是够用了。DDL DML的定义基本上是还需要发展的。另外如果开源出来了，广泛项目使用意义上，还需要支持UDF user defined function，实际上把mysql文档实现一遍就ok了。 **继续观望，欢迎抛砖。向开源的项目致敬。**