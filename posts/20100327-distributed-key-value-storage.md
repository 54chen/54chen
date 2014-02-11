title: 闲谈分布式key-value存储服务nuclear及其他
link: http://www.54chen.com/_linux_/distributed-key-value-storage.html
author: cc0cc
description: 
post_id: 1019
created: 2010/03/27 16:44:23
created_gmt: 2010/03/27 08:44:23
comment_status: open
post_name: distributed-key-value-storage
status: publish
post_type: post

# 闲谈分布式key-value存储服务nuclear及其他

现在很多国内公司都纷纷开始了key-value的nosql存储方式，然而，从什么时候开始key-value会变得这么流行呢？是风靡一时，还是顺应时代的潮流？前后数一数，有豆瓣网的beandb、有新浪的SDD、小道消息还有腾讯的TDB以及人人网的nuclear。再数国外，吹起这阵风的原因是亚马逊的一篇文档，这篇文档讲述了在亚马逊的S3服务中所使用的存储系统dynamo实现方式，但遗憾的是dynamo并不开源。紧随其后，facebook来了位号称是当年亚马逊dynamo的开发人员之一的同志，实现了facebook的cassandra，并且值得表扬的是还将其开源了。与此同时，相同理论下产生的，还有linkedin的voldemort系统。 **百家争鸣还是百家讲坛** 分布式存储的目标，是解决大规模数据在数据量不断增长的情况下，让服务更加稳定，更容易扩展。 其主要具备以下几个特点： 1.高可靠性：系统能够长时间高效运行不迭机。严格的说即使坏了一部分机器也没事。 2.可扩展性：可以随意增加减少机器，不用担心额外的数据损失。 3.负载均衡：要保证每个节点的数据都是负载均衡的，不出现集中负载到一个节点的情况。 4.一致性：因为是分布式的节点，就需要保证节点与节点之间保存数据的一致。 鱼与熊掌不可兼得，这几点，往往完成了其中几点就会损失另外一点，要全部达到完美，是一件非常困难的事情。 在国内的几个存储来看，基本都是只实现了其中的一部分，再按照自己业务的需求，来加强其中更为关心的建设。 beansdb的最终一致性通过哈希树实现快速完整数据同步（短时间内数据可能不一致）；可以在不中断服务的情况下进行容量扩展；异步IO和高性能的KeyValue数据TokyoCabinet：通过N,W,R进行配置（这点其实是dynamo的文档里的方案，并非beansdb所创）；Memcache兼容协议，大量可用客户端。sdd也大同小异。 Nuclear完成了这些功能，并且可以适配到mysql\tc\bdb等存储引擎之上。 为什么已经有开源的项目，还要去自己实现呢？简单的说，twitter敢用，你敢用吗？ **分布式key-value存储之所以稳定的原因** 从设计之初，注定这个系统会很稳定。为什么呢，主要是下面几点： 1.dynamo文档中的NWR的观点，可以让节点在损坏的情况下也能稳定如新。基本上这些系统都实现了。 2.dynamo文档中的两层数据节点的观点，可以让各节点在大负载的情况下负载均衡。部分实现了这一点。 3.底层存储的时候是key-value的读和取，只有一个维度的底层操作，对引擎来说，所有的操作都是可计算时间的。这一点的意思是说，假设都是MYSQL的底层存储，这个系统只会有一堆的select value form table where key=num这样的查询，而不会出现select * from table where key in(num1,num2,num3....)这样的查询，这两个查询不同的地方在于，如果都是1000次的查询，那么前一条的时间是可以准确预估的，而后一条取决于mysql底层实现的逻辑，而这个逻辑对上层是不可见的。 更多不明原因。。。 **适合使用的范围** 这套系统也不是放之四海而皆准的东西，如果说您的系统有如下的特征，可以考虑考虑： 1.数据插入后不需要各维度的查询。 2.数据不需要100%精确立即展示。 更多不明特点。 **总结** 其实这个系统，在过十亿的数据量下才有意思。 本文作者： 54chen(陈臻)，人人网分布式存储研究人员，业余时间混迹于各技术组织且乐此不疲。目前关注实施PHP培训。对flex等前端技术有一点研究。 个人技术站点:<http://www.54chen.com/> 。可以通过电子邮件 czhttp@gmail.com 联系到他。 **[文中所提及的链接]** beansDB:http://code.google.com/p/beansdb/ SDD:http://code.google.com/p/sina-sdd/ Nuclear:http://ugc.renren.com S3:http://aws.amazon.com/s3/ dynamo:http://s3.amazonaws.com/AllThingsDistributed/sosp/amazon-dynamo-sosp2007.pdf cassandra:http://incubator.apache.org/cassandra/ voldemort:http://project-voldemort.com/

## Comments

**[lanye](#12195 "2010-03-29 11:15:14"):** 看看看看看看

**[cc0cc](#12196 "2010-03-29 14:27:42"):** 嗯 坚定不移地低调一点

