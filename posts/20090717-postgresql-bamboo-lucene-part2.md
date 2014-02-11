title: [原创]使用postgreSQL+bamboo搭建比lucene方便N倍的全文搜索 第二部分
link: http://www.54chen.com/_linux_/postgresql-bamboo-lucene-part2.html
author: cc0cc
description: 
post_id: 634
created: 2009/07/17 15:21:09
created_gmt: 2009/07/17 07:21:09
comment_status: open
post_name: postgresql-bamboo-lucene-part2
status: publish
post_type: post

# [原创]使用postgreSQL+bamboo搭建比lucene方便N倍的全文搜索 第二部分

[文章作者：陈臻 本文版本：v1.0 最后修改：2009.7.17 转载请注明原文链接：<http://www.54chen.com/_linux_/postgresql-bamboo-lucene-part2.html> ] 书接上回。上回说[到建立好一整套的中文分词和pgsql的环境](/c/601)，这回来说如何搜。 **一、基础篇 ** 本回从一条sql开始： select * from dbname where field_name @@ 'aa|bb' order by rank(field_name, 'aa|bb'); 从这个sql字面意思讲解：从 dbname这个表中查field_name匹配aa或者是bb的词，并且按照他们的匹配的RANK排序。 基本上明白上面这段话后，来学习四个概念：**tsvector、****tsquery、**@@ 、**gin。** 1.**tsvector:** 在postgreSQL 8.3自带支持全文检索功能，在之前的版本中需要安装配置tsearch2才能使用。它提供两个数据类型（tsvector,tsquery），并且通过 动态检索自然语言文档的集合，定位到最匹配的查询结果，tsvector正是其中之一。 一个tsvector的值是唯一分词的分类列表，把一话一句词格式化为不同的词条，在进行分词处理的时候，tsvector会自动去掉分词中重复的词条，按照一定的顺序装入。例如 SELECT 'a fat cat sat on a mat and ate a fat rat'::tsvector; tsvector \---------------------------------------------------- 'a' 'on' 'and' 'ate' 'cat' 'fat' 'mat' 'rat' 'sat' 通过tsvector把一个字符串按照空格进行分词，这可以把分词后的词按照出现的次数排成一排（还会按词长度）。 对于英文和中文的全文检索我们还要看下面这条sql: SELECT to_tsvector('english', 'The Fat Rats'); to_tsvector \----------------- 'fat':2 'rat':3 to_tsvector函数来是tsvector规格化的，在其中可指定所使用的分词。 2.**tsquery：** 顾名思义,tsquery,表示的应该是查询相关的.tsquery是存储用于检索的词条.并且可以联合使用boolean 操作符来连接, & (AND), | (OR), and ! (NOT). 使用括号(),可以强制分为一组. 同时,tsquery 在做搜索的时候,也可以使用权重,并且每个词都可以使用一个或者多个权重标记,这样在检索的时候,会匹配相同权重的信息.跟上面的tsvector相同,tsquery也有一个to_tsquery函数. 3.**@@：** 在postgresql中全文检索匹配操作使用@@ [操作符](http://www.54chen.com/c/441),如果一个 tsvector(document) 匹配到 tsquery(query)则返回true. 看一个简单的例子： SELECT 'a fat cat sat on a mat and ate a fat rat'::tsvector @@ 'cat & rat'::tsquery; ?column? \---------- t 我们在处理索引的时候还是要使用他们的函数如下： SELECT to_tsvector('fat cats ate fat rats') @@ to_tsquery('fat & rat'); ?column? \---------- t 并且操作符 @@ 可以使用text作为tsvector和tsquery.如下操作符可以使使用的方法 tsvector @@ tsquery tsquery  @@ tsvector text @@ tsquery text @@ text 上面的前两种我们已经使用过了,但是后两种, text @@ tsquery 等同于 to_tsvector(x) @@ y. text @@ text 等同于 to_tsvector(x) @@ plainto_tsquery(y).（~）plainto_tsquery在后面讲。。。 **4.gin:** gin是一种索引的名称，全文索引用的。 我们可以通过创建gin索引来加速检索速度.例如 CREATE INDEX pgweb_idx ON pgweb USING gin(to_tsvector('english', body)); 创建索引可以有多种方式.索引的创建甚至可以连接两个列: CREATE INDEX pgweb_idx ON pgweb USING gin(to_tsvector('english', title || body)); **二、提高篇** 基础知识学完了，应该上阵了，为了实现全文检索，我们需要把一个文档创建一个tsvector 格式，并且通过tsquery实现用户的查询，在查询中我们返回一个按照重要性排序的查询结果。 先看一个to_tsquery的sql: SELECT to_tsquery('english', 'Fat | Rats:AB'); to_tsquery \------------------ 'fat' | 'rat':AB 可以看出，to_tsquery函数在处理查询[文本](http://www.54chen.com/c/358)的时候，查询文本的单个词之间要使用逻辑操作符（& (AND), | (OR) and ! (NOT)）连接（或者使用括号）。 如果执行下面这条sql就会出错： SELECT to_tsquery('english', 'Fat  Rats'); plainto_tsquery函数却可以提供一个标准的tsquery，如上面的[例子](http://www.54chen.com/c/216)，plainto_tsquery会自动加上逻辑&操作符。 SELECT plainto_tsquery('english', 'Fat  Rats'); plainto_tsquery \----------------- 'fat' & 'rat' 但是plainto_tsquery函数不能够识别逻辑操作符和权重标记。 SELECT plainto_tsquery('english', 'The Fat & Rats:C'); plainto_tsquery \--------------------- 'fat' & 'rat' & 'c' **三、终结篇** 看完上面的一堆后，千言万语汇成一句话，本文主要讲的是一条sql，在加了第一部分里所讲述的扩展后，使用下面的sql，从一个字段中搜一句话，还要排序出来： select * from tabname where to_tsvector('chinesecfg',textname) @@ plainto_tsquery('搜点啥') order by ts_rank(to_tsvector('chinesecfg',textname),plainto_tsquery('搜点啥')) limit 10; 之前的create table create index就不写了。授人以渔才是关键。

## Comments

**[ppg](#12427 "2010-06-10 11:39:29"):** 刚开始学习postgresql,博主说的暂时看不懂

**[江江](#10682 "2009-07-29 08:54:02"):** 说句实话，我是没看懂的，pgsql真的比mysql好吗

**[Gary LAM](#13020 "2010-09-01 10:56:57"):** 因為英文和中文的result不同, 所以不能用 Bamboo search 英文 SELECT plainto_tsquery('chinesecfg', 'The Fat & Rats:C') _____________________________________________________________ "'the' & 'fat' & '&' & 'rats' & ':' & 'c'" SELECT plainto_tsquery('english', 'The Fat & Rats:C') _____________________________________________________________ "'fat' & 'rat' & 'c'"

**[adex](#11868 "2009-09-09 11:55:35"):** 实话说，我看懂了，受教，pgsql真的比mysql好很多的

**[cc0cc](#11869 "2009-09-09 16:12:10"):** 恭喜你看懂了 呵呵

**[thief](#12123 "2010-01-18 14:41:31"):** 很早我就在用postgresql了，前端时间也想用tsearch做搜索，跟xapian做了一下性能测试，kw级以上的，xapian的速度远远要胜过tsearch，chen大是如何优化的呢？

