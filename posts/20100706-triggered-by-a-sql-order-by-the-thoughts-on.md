title: 一条SQL引发的对order by的思考
link: http://www.54chen.com/architecture/triggered-by-a-sql-order-by-the-thoughts-on.html
author: 54chen
description: 
post_id: 1229
created: 2010/07/06 18:27:27
created_gmt: 2010/07/06 10:27:27
comment_status: open
post_name: triggered-by-a-sql-order-by-the-thoughts-on
status: publish
post_type: post

# 一条SQL引发的对order by的思考

![热](http://img02.taobaocdn.com/imgextra/i2/T18I0FXoFCXXcVYX39_072328.jpg) ==========尽职的安静的分隔线=========== 在实际工作中遇到下面一个问题： 有一个表，存有2000万数据。 主键为ID bigint(20) NOT NULL auto_increment 另有一字段time timestamp NOT NULL default CURRENT_TIMESTAMP 故事从这两个字段说起： sql1需要从这个表中检索出来时间为2010-05-26 11:55:00之前并且id号大于20000的前10条数据 sql2需要从这个表中检索出来时间为2010-05-26 11:55:00之后并且id号大于20000的前10条数据 两条sql写出来大概是这样子的： sql1:select * from table where time <'2010-05-26 11:55:00' and id>20000 order by id limit 10; sql2:select * from table where time >'2010-05-26 11:55:00' and id>20000 order by id limit 10; 并且已经知道表中的数据，在上面所示时间之前的数据要远远多于所示时间之后的数据。如图1所示： ![54chen](http://img05.taobaocdn.com/imgextra/i5/T1AKhFXgdbXXaH.kQ3_051000.jpg) 图1 数据在时间线上的示意图 实测发现，sql1执行时间0.03s，sql2执行时间33s。 为何大于小于运行的速度相比如何巨大？下面来解答。 **第一，用explain来观察两条sql的区别** 结论：没什么区别 **第二，研究order by** 将sql2的order by id修改为order by id desc(排序方向颠倒)后，发现速度马上提到了0.03s的水平。 同样修改sql1的时候，速度马上降到了30s的水平。 进行多次测试，排除mysql本身的缓存干扰。 结论： sql1的运行示意图如图2所示： ![54chen](http://img06.taobaocdn.com/imgextra/i6/T1ouhFXgXdXXbLbqg6_060541.jpg) 图2 第一条SQL语句快慢解释图 sql2的运行示意图如图3所示： ![54chen](http://img02.taobaocdn.com/imgextra/i2/T14KdFXc0eXXc_k476_061836.jpg) 图2 第二条SQL语句快慢解释图 综合上面两个图，mySQL在where查询的时候，也许按照where的条件，按照主键的顺序，最后满足条件的，最后进到内存中去，再进行后面的order by时，asc如果在内存中比不在内存中的就要快得多。 未研究真正实现的代码，仅凭感觉验证。 一句话概括是：按照使用的索引，最后满足条件的数据将留在内存里供进一步排序。

## Comments

**[cfanbo](#13522 "2011-04-21 18:47:50"):** 好像与这个有关系的,orderby排序的问题

**[sk](#13505 "2011-04-14 11:43:48"):** 这个是排序的问题 默认orde by是升序，sql1 升序的顺序恰好与id在索引中的顺序一样

**[jason](#13791 "2011-09-12 10:09:08"):** 感觉是一个窗口从前往后(asc)或从后往前扫(desc)，一直到包含limit个符合条件的就行了。不是最后才排序。

**[54chen](#12496 "2010-07-08 16:19:35"):** 颠倒这个试过，没有明显的变化

**[aaa](#12497 "2010-07-09 01:56:46"):** 如果把< 换成 < = 会更快点. 二分查找 算法

**[fanlor](#12498 "2010-07-09 07:39:36"):** 没看太明白 ，where 后面的条件 ，也就是and 前后 条件的顺序 会不会有关系，是从左到右，还是从右到左呢？

**[luguo](#12486 "2010-07-06 23:25:46"):** 文章没看，题图大亮！

**[airwin](#12487 "2010-07-07 09:35:24"):** 不太明白，是说因为SQL2的查询结果按照主键递增的顺序装入内存(假设类似于数组入栈那种)，而asc排序需要从栈顶从头排一遍所以慢？

**[54chen](#12490 "2010-07-07 12:28:32"):** 大概意思是说，在 排序方向+limit的数据在内存里的快，在内存外的所以慢

**[四不象](#12491 "2010-07-07 23:05:54"):** 其实就是全表扫描的问题。 time字段没做索引，所以会引发全表扫描，sql1扫描数据少，所以快，sql2扫描数据多，所以慢，仅此而已。

**[54chen](#12499 "2010-07-09 13:54:34"):** 这个实测过程中不是很明显

**[54chen](#12493 "2010-07-08 08:12:48"):** 您没有仔细看题目，sql1 变成desc时也会奇慢无比，而且事实上sql1的数据多

**[auzll](#12494 "2010-07-08 08:30:08"):** sql1:select * from table where id>20000 and time 20000 and time >’2010-05-26 11:55:00' order by id limit 10; \--我觉得换一下where后面的条件，把id放在time之前会好一些吧？

**[auzll](#12495 "2010-07-08 08:33:49"):** 我上面的代码格式错了。。。反正我的意思是把 where后面那两个条件的次序调整一下吧，id在前，time在后

**[54chen](#12500 "2010-07-09 13:55:33"):** 颠倒顺序试过没有显示改变

**[求解答](#12521 "2010-07-23 17:53:59"):** 锁表内容: mysql> SHOW PROCESSLIST; +--------+----------+-----------------+----------+---------+------+----------------+------------------------------------------------------------------------------------------------------+ | Id | User | Host | db | Command | Time | State | Info | +--------+----------+-----------------+----------+---------+------+----------------+------------------------------------------------------------------------------------------------------+ | 174133 | root | localhost | NULL | Query | 0 | NULL | SHOW PROCESSLIST | | 177472 | bdrsU | localhost | bdrsU | Sleep | 913 | | NULL | | 177473 | bdrsU | localhost | bdrsU | Sleep | 913 | | NULL | | 179302 | root | 127.0.0.1:60397 | bdrsU | Query | 158 | Writing to net | SELECT id, classid, className, uid, schoolName, shortName, title, keyword, content, staff, traffic, | | 179366 | bdrsU | localhost | bdrsU | Query | 126 | Table lock | UPDATE core_course SET pv=pv+1 WHERE id='24138' | | 179369 | bdrsU | localhost | bdrsU | Query | 125 | Table lock | SELECT * FROM `core_course` WHERE mid = 'a14cfc2e3dd343a4' | | 179382 | bdrsU | localhost | bdrsU | Query | 119 | Table lock | SELECT count(*) FROM core_course WHERE aid = 248 AND classid IN (380,381,382,630,631,632) AND delete | | 179384 | bdrsU | localhost | bdrsU | Sleep | 119 | | NULL | | 179387 | bdrsU | localhost | bdrsU | Query | 117 | Table lock | SELECT count(*) FROM core_course WHERE aid = 256 AND classid IN (641,27,33,32,31,23,21,36,29,34,25,2 | | 179389 | bdrsU | localhost | bdrsU | Sleep | 117 | | NULL | | 179396 | bdrsU | localhost | bdrsU | Query | 112 | Table lock | SELECT count(*) FROM core_course WHERE aid = 450 AND classid IN (366) AND deleted = 'N' AND userdel | | 179398 | bdrsU | localhost | bdrsU | Sleep | 112 | | NULL | | 179399 | bdrsU | localhost | bdrsU | Query | 111 | Table lock | SELECT * FROM `core_course` WHERE id = '29839' | | 179403 | bdrsU | localhost | bdrsU | Query | 109 | Table lock | SELECT * FROM `core_course` WHERE id = '29839' | | 179408 | bdrsU | localhost | bdrsU | Query | 107 | Table lock | SELECT * FROM `core_course` WHERE id = '29842' | | 179409 | bdrsU | localhost | bdrsU | Query | 107 | Table lock | SELECT * FROM core_course WHERE aid ='3' AND deleted = 'N' AND userdel = 'N' AND audit = 'Y' ORDER B | | 179413 | bdrsU | localhost | bdrsU | Query | 105 | Table lock | SELECT * FROM `core_course` WHERE id = '29839' | | 179422 | bdrsU | localhost | bdrsU | Query | 101 | Table lock | SELECT count(*) FROM core_course WHERE aid = 249 AND classid IN (67,69,68,70,74,72,73,71) AND delete | | 179423 | bdrsU | localhost | bdrsU | Sleep | 101 | | NULL | | 179426 | bdrsU | localhost | bdrsU | Query | 100 | Table lock | SELECT * FROM `core_course` WHERE mid = '876206874f815efd' | | 179447 | bdrsU | localhost | bdrsU | Query | 88 | Table lock | SELECT count(*) FROM core_course WHERE aid = 558 AND classid IN (364) AND deleted = 'N' AND userdel | | 179448 | bdrsU | localhost | bdrsU | Sleep | 88 | | NULL | | 179457 | bdrsU | localhost | bdrsU | Query | 75 | Table lock | SELECT count(*) FROM core_course WHERE aid = 341 AND classid IN (348,346) AND deleted = 'N' AND user | | 179459 | bdrsU | localhost | bdrsU | Query | 81 | Table lock | SELECT * FROM `core_course` WHERE mid = '312819e3189b3dd8' | | 179471 | bdrsU | localhost | bdrsU | Sleep | 75 | | NULL | | 179474 | bdrsU | localhost | bdrsU | Query | 73 | Table lock | SELECT count(*) FROM core_course WHERE aid = 657 AND classid IN (502,503,504,524,505) AND deleted = | | 179477 | bdrsU | localhost | bdrsU | Sleep | 73 | | NULL | | 179481 | bdrsU | localhost | bdrsU | Query | 71 | Table lock | select classid,core_column.mid,core_column.typename,core_column.id from core_course left join core_c | | 179482 | bdrsU | localhost | bdrsU | Sleep | 71 | | NULL | | 179500 | bdrsU | localhost | bdrsU | Query | 63 | Table lock | SELECT * FROM core_course WHERE aid ='549' AND deleted = 'N' AND userdel = 'N' AND audit = 'Y' ORDER | | 179558 | bdrsU | localhost | bdrsU | Query | 32 | Table lock | select classid,core_column.mid,core_column.typename,core_column.id from core_course left join core_c | | 179565 | bdrsU | localhost | bdrsU | Query | 27 | Table lock | SELECT count(*) FROM core_course WHERE aid = 426 AND classid IN (12 AND deleted = 'N' AND userdel | | 179568 | bdrsU | localhost | bdrsU | Sleep | 27 | | NULL | | 179569 | bdrsU | localhost | bdrsU | Query | 27 | Table lock | SELECT * FROM `core_course` WHERE id = '29839' | | 179573 | bdrsU | localhost | bdrsU | Query | 25 | Table lock | select classid,core_column.mid,core_column.typename,core_column.id from core_course left join core_c | | 179574 | bdrsU | localhost | bdrsU | Sleep | 25 | | NULL | | 179601 | root | localhost | bdrsU | Query | 12 | Table lock | SELECT * FROM `core_course` LIMIT 0, 30 | | 179609 | bdrsU | localhost | bdrsU | Query | 8 | Table lock | SELECT * FROM `core_course` WHERE id = '29839' | | 179618 | bdrsU | localhost | bdrsU | Query | 4 | Table lock | SELECT * FROM `core_course` WHERE id = '29839' | | 179621 | bdrsU | localhost | bdrsU | Query | 2 | Table lock | SELECT * FROM `core_course` WHERE id = '29839' | | 179623 | bdrsU | localhost | bdrsU | Query | 2 | Table lock | SELECT * FROM `core_course` WHERE id = '29839' |

**[lavadiablo](#12550 "2010-08-04 16:56:10"):** 嗯

**[54chen](#12551 "2010-08-04 17:17:52"):** 猜你的表是myiasm的 换成innodb试试

**[哇哈哈哈](#12561 "2010-08-06 18:57:39"):** 我是来看图的。。。。

