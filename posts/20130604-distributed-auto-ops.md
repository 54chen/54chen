title: 从分布式存储设计到自动化运维
link: http://www.54chen.com/architecture/distributed-auto-ops.html
author: 54chen
description: 
post_id: 2331
created: 2013/06/04 20:35:43
created_gmt: 2013/06/04 12:35:43
comment_status: open
post_name: distributed-auto-ops
status: publish
post_type: post

# 从分布式存储设计到自动化运维

![分布式存储](http://img02.taobaocdn.com/bao/uploaded/i2/T11wdBXn8gXXbWvNA4_052909.jpg) <http://www.infoq.com/cn/articles/nosql-dynamo> 三年前在infoq发表的一篇关于两种特别有代表性的分布式存储的设计思路解析，三年过去了，今天再来总结看看这几年的变化。 实际上，这三年，还是两个东西，一直没有冒出来更牛B的东西。 **一、dynamo代表作riak特点** 早几年以cassandra为代表此类项目，固定特点为：水平扩展、无中心节点、多备份、最终一致性、性能一般、适合海量数据。因为cassandra在业界的使用失败案例太多，让大家避而远之。这两年，以erlang开发的riak又冒出水面。 **1.1 erlang** 这作为riak的最大特点一点也不为过，因为语言在分布式领域的独特能力，使得riak的源代码十分简洁干净。不过一万多行的代码，在第一次读到它的代码时，我也感叹，几年前，傻希希的用java代码堆了十几万行的nuclear代码，真是太笨了。 **1.2 完整的dynamo实现** 在cassandra的年代，许多东西不方便实现，版本控制的向量时钟使用了timestamp代替，vnode在cassandra上是非常大的区块，在进行负载均衡时有很大可能不均匀。到了riak的时代，所有的特点，在erlang的支持下，完成了各种细节。并且增加了：1.http存取的支持。2.双向索引。3.搜索支持。4.m/r支持。 **二、bigtable代表作hbase特点** 与dynamo对应的解决方案bigtable的历史更加悠久一些，开源项目也进行了很多年，hbase社区也正在不断地完善。 **1.1 偷懒地依赖hdfs** 严格说来hbase的实现，只主要关心了regionServer（中心节点所在，用来分配数据所在位置），所以说偷懒地在底下使用了hdfs完成备份工作。 **1.2 列簇** 在借用hdfs之后，在其上实现的存储格式让hbase可以满足各式各样的需求，当然了，这么复杂的交互，最好还是使用ssd之类的高速度的存储介质。 **三、发展方向及特点** 在回顾了两大阵营的各自特点之后，再来看看未来。 **3.1 mysql时代** 招一堆的mysql dba，指哪打哪，哪坏修哪。工作得很好! **3.2 nosql时代** 开发工程师了解了dba的苦逼，以及老板招不到dba的苦逼，决定将数据结构化，简化代码的数据结构。 典型的代表key-value系统。 再基于这些单一的结构，做一堆的自动加机器自动转数据的功能。riak在此列。hbase略高于此。 **3.3 未来时代** 不仅是存储，整个运维工作，都应该是自动化演进，你可以想象：一个晴朗的下午，工程师带着耳机听着歌，将需求模型输入之后，一个红按钮一按，代码已经写好，test自动开始，AB test，staging，一切OK，自动分发到了各处。上线五分钟，某处开始报警，中央自动判断如何添加机器，执行添加。 －－写在32位服务器已经过时了的日子，纪念一下，中国都应该记住。