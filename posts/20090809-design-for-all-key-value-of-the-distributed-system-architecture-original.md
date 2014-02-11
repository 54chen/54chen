title: 为人人设计的分布式key-value系统架构[原创]
link: http://www.54chen.com/architecture/design-for-all-key-value-of-the-distributed-system-architecture-original.html
author: cc0cc
description: 
post_id: 714
created: 2009/08/09 19:12:07
created_gmt: 2009/08/09 11:12:07
comment_status: open
post_name: design-for-all-key-value-of-the-distributed-system-architecture-original
status: publish
post_type: post

# 为人人设计的分布式key-value系统架构[原创]

[作者：陈臻 转载请注明出处：<http://www.54chen.com/714-design-for-all-key-value-of-the-distributed-system-architecture-original/> 版本：1.1  2090810] 8.10 增加dev4server组里esx大侠提出的几个代表性问题 这个架构的产生，是为了公司的一个新项目，而后来慢慢变成了解决整个公司的所有问题的一个架构，期间经yahoo的angentZh先生、dev4server组里张立冰先生、盛大的许式伟先生推荐研究了bigtable、Dynamo等很有代表性的分布式架构。 如下图所示： ![](/wp-content/uploads/2009/08/e7bb98e59bbe1.jpg) 总体：底层以key-value存储，每个节点内作主主互备，节点以[一致性哈希](http://www.54chen.com/705-peer-to-peer-distributed-hash-algorithm-in-the-mainstream-of-comparative-analysis-of-the-collection/)存取，哈希所使用的key为relation-key，非直接存取时的key。 step 0：连接客户端收到一个key为relatioin-key_id的存取请求，取出relation-key进行一致性哈希计算，这里是为了让相关的内容都能存在一个节点上，类似bigtable的tablet； step 1：连接客户端读取最新的配置文件，server.conf。 step 2：根据配置文件寻找正确的节点。 step 3：在B节点之间增加了一个节点A的时候，A前的虚拟[节点](http://www.54chen.com/399-yahoo%e3%80%81tao%e4%ba%91%e8%ae%a1%e7%ae%97%e5%88%a9%e5%99%a8%e4%b9%8b%e2%80%9c%e4%ba%91%e2%80%9d%e7%ab%af%e7%9a%84%e5%b0%8f%e9%a3%9e%e8%b1%a1%e2%80%94hadoop/)将寻找不到数据，此时连接客户端会重新读取老的配置文件server.conf.1，根据老的配置，这里的数据会去B节点读取原来的数据，读到的数据会转移到新增加的节点A中。 step 4：增加节点A后，[服务器](http://www.54chen.com/379-%e8%bd%bb%e7%82%b9%e9%ad%94%e6%a3%92%ef%bc%8c%e7%9e%ac%e9%97%b4%e5%ae%89%e8%a3%85%e4%b8%8a%e7%99%be%e5%8f%b0%e6%9c%8d%e5%8a%a1%e5%99%a8/)端会同时运行手动的转移脚本，转移脚本直接将B节点中符合A节点的存取规则的数据全部转移，转移结束将操作server.conf.1，进行删除老配置文件的操作。 这个架构的特点： 1.底层的key-value引擎并不特指某种，用cabinet或者是mysql都是可以的； 2.增加或者删除节点都可以是半自动+半手动或者是全手动处理； 3.适合大多数大型网站任何业务。 这个架构的名字：未命名 1\. 速度：relation-key存储的方式，使相关性强的数据都在一起，保障批量的速度； 2.容灾：底层master-master同步的DB保障了其中一台出现故障不会影响整个系统； 3.扩展：手动加自动的方式使扩展节点应对自如。 

### Q&A:

Q:如何发现是找不到数据，而不是数据本来就没有？ A:系统中有server.conf server.conf.1 server.conf.2....只检测历史配置文件，如果手动迁移数据结束，历史配置文件将被删除。 Q:节点A进入时，是否能有选择的向B所要数据？ A:这里的确是忽略了同一个key的数据的版本控制的问题。如果先执行了手动脚本再存到A结点是正常的，如果先存到A再执行手动脚本，会出现老数据盖了新数据。不知有啥好的idea没? Q:所要数据后，何时算完成？因为B可能一直在有新数据生成。 A:如果是新的配置文件上来了，写入B的数据应该就已经是新的规则了，这样，只需要手动执行的脚本循环当前的数据一圈，其中的数据自然是正确无误的了。手动脚本完成后删除老的配置文件标志迁移结束。 Q:完成后，节点A如何生效？ A:老的配置文件删除前，读数据操作是半生效状态（逐步恢复）；老的配置文件被删除后，A节点的读写都自然生效了。 Q:容灾，如果节点A掉了，那B上是否有A所保存的信息？ A:一个节点下有至少两个物理实际节点做主主备份，上面是一个带网络检测和自动选取连接的工具，虚拟成一个节点，换句话说，A节点两台机器全部坏掉的可能性这里视为零。

## Comments

**[Tim](#10959 "2009-08-10 12:02:08"):** 不知道文中所说虚拟节点如何实现的，否则感觉像和直接用多台tc没什么大区别。

**[cc0cc](#10960 "2009-08-10 14:08:16"):** 兄弟是指两台tc上的那个Zfor吗?详细见 http://github.com/lenn0x/zfor/tree/a71567de0424ee5d3ff15471b8d04c5ec3b74e18

**[Tim](#10992 "2009-08-11 16:55:08"):** 原来你的虚拟节点不是dynamo那个概念，误解了。 Q2可以用timestamp比较解决吧。 另外有个担心，新插入节点时候A时，A从B复制数据会导致B压力过大，Dynamo里面专门讨论了这个问题。6.2 strategy 1

**[cc0cc](#10993 "2009-08-11 17:02:06"):** 这个系统今天和公司的大侠们研究了下，又有演变了，zfor作为一个单独的服务，有不稳定的嫌疑，所以我考虑把zfor去掉或者是用什么办法，将原来的一个节点变成两个节点。 Dynamo也建议在备份的时候选择系统压力小的时候，应该都会遇到这个问题。不可避免，只有选择在半夜进行这种操作。

**[ddd](#10994 "2009-08-11 18:19:54"):** 我就不信我认不出验证码

