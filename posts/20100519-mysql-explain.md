title: mysql explain详解
link: http://www.54chen.com/architecture/mysql-explain.html
author: 54chen
description: 
post_id: 1150
created: 2010/05/19 17:52:55
created_gmt: 2010/05/19 09:52:55
comment_status: open
post_name: mysql-explain
status: publish
post_type: post

# mysql explain详解

![mysql explain](http://img07.taobaocdn.com/bao/uploaded/i7/T1cRdAXeVlXXbDaB72_044910.jpg)

doubanclaimed89ad216b01beaf 题注：感谢suchasplus兄弟在第一时间提示explain拼写错误~~~:) 对于经常使用mysql的兄弟们，对explain一定不会陌生。当你在一条SELECT语句前放上关键词EXPLAIN，MySQL解释它将如何处理SELECT，提供有关表如何联合和以什么次序的信息。 借助于EXPLAIN，你可以知道 1)你什么时候必须为表加入索引以得到一个使用索引找到记录的更快的SELECT。 2)你也能知道优化器是否以一个最佳次序联结表。为了强制优化器对一个SELECT语句使用一个特定联结次序，增加一个STRAIGHT_JOIN子句。 官方的关于explain的文档在http://dev.mysql.com/doc/refman/5.1/en/using-explain.html（英文），本文可作为官方文献的不完整通俗读物。 

## **使用的方法**

EXPLAIN tbl_name 或： EXPLAIN [EXTENDED] SELECT select_options 前者可以得出一个表的字段结构等等，后者主要是给出相关的一些索引信息，而今天要讲述的重点是后者。 

## 举例

mysql> explain select * from event; +----+-------------+-------+------+---------------+------+---------+------+------+-------+ | id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra | +----+-------------+-------+------+---------------+------+---------+------+------+-------+ | 1 | SIMPLE | event | ALL | NULL | NULL | NULL | NULL | 13 | | +----+-------------+-------+------+---------------+------+---------+------+------+-------+ 1 row in set (0.00 sec) 

## **各个属性的含义**

id select查询的序列号 select_type select查询的类型，主要是区别普通查询和联合查询、子查询之类的复杂查询。 table 输出的行所引用的表。 type 联合查询所使用的类型。 type显示的是访问类型，是较为重要的一个指标，结果值从好到坏依次是： system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL 一般来说，得保证查询至少达到range级别，最好能达到ref。 possible_keys 指出MySQL能使用哪个索引在该表中找到行。如果是空的，没有相关的索引。这时要提高性能，可通过检验WHERE子句，看是否引用某些字段，或者检查字段不是适合索引。 key 显示MySQL实际决定使用的键。如果没有索引被选择，键是NULL。 key_len 显示MySQL决定使用的键长度。如果键是NULL，长度就是NULL。文档提示特别注意这个值可以得出一个多重主键里mysql实际使用了哪一部分。 ref 显示哪个字段或常数与key一起被使用。 rows 这个数表示mysql要遍历多少数据才能找到，在innodb上是不准确的。 Extra 如果是Only index，这意味着信息只用索引树中的信息检索出的，这比扫描整个表要快。 如果是where used，就是使用上了where限制。 如果是impossible where 表示用不着where，一般就是没查出来啥。 如果此信息显示Using filesort或者Using temporary的话会很吃力，WHERE和ORDER BY的索引经常无法兼顾，如果按照WHERE来确定索引，那么在ORDER BY时，就必然会引起Using filesort，这就要看是先过滤再排序划算，还是先排序再过滤划算。 

## **常见的一些名词解释**

Using filesort MySQL需要额外的一次传递，以找出如何按排序顺序检索行。 Using index 从只使用索引树中的信息而不需要进一步搜索读取实际的行来检索表中的列信息。 Using temporary 为了解决查询，MySQL需要创建一个临时表来容纳结果。 ref 对于每个来自于前面的表的行组合，所有有匹配索引值的行将从这张表中读取 ALL 完全没有索引的情况，性能非常地差劲。 index 与ALL相同，除了只有索引树被扫描。这通常比ALL快，因为索引文件通常比数据文件小。 SIMPLE 简单SELECT(不使用UNION或子查询) 

## **关于哥学社**

[哥学社](/blog-brother)是一个自由博客组织，以提高原创技术博客质量，为成员博客增加更有效评论，由来自腾讯、百度、盛大、人人网、新浪、奇虎、TOM等各大企业的哥们共同起草发起。 

## **关于五四陈**

54chen(陈臻)，人人网分布式存储研究人员，业余时间混迹于各技术组织且乐此不疲。目前关注实施PHP培训。对flex等前端技术有一点研究。 个人技术站点:<http://www.54chen.com/> 。可以通过电子邮件 czhttp@gmail.com 联系到他。 

## **本文提及的文章**

<http://dev.mysql.com/doc/refman/5.1/en/using-explain.html> <http://dev.mysql.com/doc/refman/5.1/zh/optimization.html#explain>

## Comments

**[suchasplus](#12342 "2010-05-19 18:18:56"):** 赞本文题目!

**[54chen](#12343 "2010-05-19 18:31:37"):** 上班时间手误，多谢兄弟的赞，这就更新过来

**[丽水生活网](#12467 "2010-06-28 12:47:53"):** 这个对于性能优化，合理建立索引太重要了~ 一个合理的索引可以减少很多查询啊~

