title: riak源码阅读手记 与Cassandra相比
link: http://www.54chen.com/architecture/riak-vs-cassandra.html
author: 54chen
description: 
post_id: 1820
created: 2011/09/14 14:29:32
created_gmt: 2011/09/14 06:29:32
comment_status: open
post_name: riak-vs-cassandra
status: publish
post_type: post

# riak源码阅读手记 与Cassandra相比

![riak](http://wiki.basho.com/images/logo_wiki.png) riak源码阅读手记一 初出茅庐 项目入口 http://www.54chen.com/_linux_/riak-source.html riak源码阅读手记二 左右开弓 启动 http://www.54chen.com/_linux_/riak-cource-code.html riak源码阅读手记 运行安装 http://www.54chen.com/_linux_/riak-source-install-run.html 一些常用的指令： http://wiki.basho.com/Command-Line-Tools.html#riak-admin ** 实现上：** 1）虽然二者都是dynamo的实现，具体方法还是不一样的，riak更忠于dynamo的原文档，实现了所有文档提到的关键点。同时还增加了map reduce和links等功能。 2）Cassandra略去了一些文档里的关键点：向量时钟、按照key范围的大分区等。增加了一些方法如：范围查询、固定分区。 **扩展：** 1） riak提供了bin/riak join这样的命令来加入新的节点，基本完全按照dynamo文档所说的一样来实现了，从多个节点去获取原来的负载和数据回来，每个节点的压力都可以得到平衡。 2）相比之下，Cassandra集群的节点需要计算数据的一个范围。当加入节点时，Cassandra的策略是分出来相应分区上一半的范围去新节点。这点在一个Cassandra集群需要加节点时会很痛苦，可能会存在两个节点之间大规模的数据转移。 **查询和分布：** 1）riak有map reduce。 2）Cassandra可以接hadoop达到M/R的效果。 **写冲突检查：** 1）riak使用了向量时钟。 2）Cassandra使用了timestamp。如果时间有问题，可能会丢。 **数据模型：** 1）riak有bucket的概念，每个buckect可动态建立，每个buckect的数据模型都不一样。 2）Cassandra的keyspace与列簇都是依赖xml定义的，如果要修改，需要重启。 **API：** 1）riak提供了http与protocol buffers两种 2）Cassandra使用thrift **可配置的存储：** 1）riak默认是bitcast存储，还有innodb，开发者很勤快，甚至还有leveldb。 2）Cassandra一直就是SSTable。

## Comments

**[54chen](#13949 "2011-11-01 19:39:12"):** cassandra是大分区策略，一旦要加节点，大量的数据会倾斜，riak不会，是碎片数据。

**[jetty](#13935 "2011-10-28 00:06:46"):** “Cassandra集群需要加节点时会很痛苦”为什么痛苦，难道riak不数据迁移。。。？

