title: 人人网海量存储系统Nuclear介绍
link: http://www.54chen.com/_linux_/nuclear-mass-storage-system-for-all-network-description.html
author: cc0cc
description: 
post_id: 929
created: 2010/01/21 18:25:25
created_gmt: 2010/01/21 10:25:25
comment_status: open
post_name: nuclear-mass-storage-system-for-all-network-description
status: publish
post_type: post

# 人人网海量存储系统Nuclear介绍

经过这几个月大家共同的努力，Nuclear终于来到新的阶段。 ![人人网 Nuclear](/wp-content/uploads/2010/01/nuclear.jpg) 山寨版本的Logo Nuclear存储系统:**高性能、高可靠、可扩展的海量数据存储** 请移步人人网UGC团队博客：<http://ugc.renren.com/2010/01/21/ugc-nuclear-guide-use/> 人人网UGC团队博客正式在[科学院](http://ugc.renren.com)亮像，欢迎网友订阅，专注UGC社区大负载研究与应用！ <http://ugc.renren.com> <http://ugc.fm> **下面是一些补习班知识：**

#### TC

[Tokyo Cabinet ](/architecture/google-big-table-similar-to-the-tokyo-cabinet-research-notes.html)是一个DBM的实现。这里的数据库由一系列key-value对的记录构成。key和value都可以是任意长度的字节序列,既可以是二进制也可以是字符串。这里没有数据类型和数据表的概念。当做为Hash表数据库使用时，每个key必须是不同的,因此无法存储两个key相同的值。提供了以下访问方法:提供key,value参数来存储，按 key删除记录，按key来读取记录，另外，遍历key也被支持，虽然顺序是任意的不能被保证。这些方法跟Unix标准的DBM,例如GDBM,NDBM 等等是相同的，但是比它们的性能要好得多，因此可以替代它们。 

#### Dynamo

[Dynamo ](/translate/i-am-the-chen-version-distributed-storage-system-documentation-translation-program-voldemort-lightcloud-dynamo.html)的可扩展性和可用性采用的都比较成熟的技术，数据分区并用改进的一致性哈希(consistent hashing)方式进行复制，利用数据对象的版本化实现一致性。复制时因为更新产生的一致性问题的维护采取类似 quorum 的机制以及去中心化的复制同步协议。 Dynamo 是完全去中心化的系统，人工管理工作很小。 **Linkedln　**　 [Linked](http://www.54chen.com/translate/dynamo-based-systems-designed-linkin-voldemort-voldemort-design-chinese-documents-i-am-a-chan-academy-of-sciences-translation-finalized.html)是一个“高效”、“安全”并且“有商务价值”的“白领SNS提供商”：LinkedIn足够高效，而且没有什么打扰用户的信息。即便是广告的投放，LinkedIn也处理的十分艺术：有侧边栏下方几乎不会分散我的注意力； LinkedIn上没有太多会打扰到你的人。而且，用户与用户之间的关系严密地保护起来。这和其他SNS拿着用户资料去做SEO实在是天壤之别； LinkedIn专注于商务功能，并且提供付费服务。这极大地体现了它的确具备提供高质量商务社交服务的能力 LinkedIn是非常适合白领使用的一款SNS工具，尤其是有国际业务的企业员工或者自由职业者。不过，Linked不太适合学生使用。因为Linked很注重工作经验和教育背景，如果你现在还在还没毕业，除非你有相当丰富的社会实践经验，否则完全可以忽视这个玩意，因为它现在还无法为你创造价值。他有一个相似的系统叫伏地魔，之前科学院里的文章曾经提起过，[起驾观看](http://www.54chen.com/translate/dynamo-based-systems-designed-linkin-voldemort-voldemort-design-chinese-documents-i-am-a-chan-academy-of-sciences-translation-finalized.html)。