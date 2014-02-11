title: 用sphinx轻松搞定方便管理的多节点过亿级数据搜索
link: http://www.54chen.com/_linux_/sphinx-data-search.html
author: 54chen
description: 
post_id: 1235
created: 2010/07/14 15:31:14
created_gmt: 2010/07/14 07:31:14
comment_status: open
post_name: sphinx-data-search
status: publish
post_type: post

# 用sphinx轻松搞定方便管理的多节点过亿级数据搜索

![54chen](http://img04.taobaocdn.com/imgextra/i4/T1tV8GXeprXXaFKjI2_044608.jpg) **概述** 来自俄罗斯的开源全文搜索引擎软件Sphinx，单一索引最大可包含1亿条记录，在1千万条记录情况下的查询速度为0.x秒（毫秒级），实测千万级数据在0.0X秒和0.00X秒占大多数。 Sphinx创建索引的速度为：创建100万条记录的索引只需3～4分钟，实测30W线上复杂的blog数据需要5分钟，创建1000万条记录的索引可以在50分钟内完成，实测时间比这个更长得多，而只包含最新10万条记录的增量索引，重建一次只需几十秒，实测十万条在一分钟不到的时间。 Sphinx 是一个基于 GPL 2 协议颁发的免费开源的全文搜索引擎.它是专门为更好的整合脚本语言和SQL数据库而设计的.当前内置的数据源支持直接从连接到的 MySQL 或 PostgreSQL 获取数据, 或者你可以使用 XML 通道结构(XML pipe mechanism , 一种基于 Sphinx 可识别的特殊xml格式的索引通道) 。 **sphinx安装** 安装见 [用Sphinx快速搭建站内搜索功能](/_linux_/sphinx-build-stations-with-fast-search-function.html) **配置多节点协同工作** 第一点，原理 在sphinx.conf中可以配置index段落里的local和agent两个参数，local = blog_1表示使用本地索引名为blog_1的索引，agent = 10.1.1.1:3312:blog_2表示使用10.1.1.1这个机器的3312端口上服务的blog_2索引。这两个参数均可在此段落中重复出现。 利用这两个参数，可进行节点与节点间的配置。如图1所示，一个searchd服务在接到请求时两种使用索引的示意图。 ![sphinx 54chen](http://img02.taobaocdn.com/imgextra/i2/T1FqFGXcBaXXcM6aMZ_031346.jpg) 图1 一个searchd服务在接到请求时两种使用索引的示意图 第二点，架构 利用agent参数，可以灵活配置去代理取满足搜索条数的超时时间等等。 如图2，只需要将索引灵活分布，各自除了自己的local索引以外，全部连成相互的agent，使得别的节点也可以得到自己的索引搜索结果，以此得到分布式的处理结果。 ![sphinx 54chen](http://img03.taobaocdn.com/imgextra/i3/T1_aBGXglbXXXdthoZ_032504.jpg) 图2 一个简单的分布式搜索的例子 第三点，性能 分布索引的情况下，实测千万数据量，大致在500qps左右，没有做容灾的方案，如果想容灾，可以考虑将索引做成在某些节点上重复。总得来说，性能还算可以，更具体的架构方案，可能要和具体的业务来分布效果会更好。

## Comments

**[sai](#15013 "2012-09-02 00:28:28"):** sphinx稳定性怎么样？小米有在线上使用的成功案例吗？

**[54chen](#15014 "2012-09-03 11:15:05"):** 还是比较可靠的。

**[dynamiclu](#14903 "2012-05-25 17:27:05"):** sphinx分布式的，用agent代理另外一台机器索引，本机没有source, 一直查不到数据，报错ERROR unknown local index ， 而本地文件有对应的配置，分布式配置失败，类似这位帖子的问题：http://www.iteye.com/problems/62292； 求正解？

**[54chen](#14906 "2012-05-28 14:53:25"):** 这些搜索解决方案大多都单机下工作很好，多机就都有问题，建议使用katta，主要是一个打分的过程是最麻烦的，否则你还是得自己去做分布的过程。

**[lxh](#13530 "2011-04-29 17:30:33"):** 能详细点不，还是不清楚如何弄sphinx分布式。

**[Timo](#12507 "2010-07-14 15:50:34"):** 作为同事友情帮顶

**[54chen](#12508 "2010-07-14 16:50:30"):** 哇，这是哪位兄弟的马甲。。。没见过说。。。

**[akwei](#12509 "2010-07-14 17:29:06"):** 一直在用lucence，这个还没尝试过，有空用下，目前没有这种大数据量和分布式的环境

**[Timo](#12510 "2010-07-15 10:44:13"):** 在你楼下

**[d_yang](#12512 "2010-07-15 13:45:14"):** 就这么多？ 陈胖子果然很会教育你们。

**[software](#12553 "2010-08-04 21:37:32"):** 不知道比lucene能快多少.

