title: [原创][实测]MySQL时间字段究竟使用INT还是DateTime？
link: http://www.54chen.com/_linux_/%e5%8e%9f%e5%88%9b%e5%ae%9e%e6%b5%8bmysql%e6%97%b6%e9%97%b4%e5%ad%97%e6%ae%b5%e7%a9%b6%e7%ab%9f%e4%bd%bf%e7%94%a8int%e8%bf%98%e6%98%afdatetime%ef%bc%9f.html
author: cc0cc
description: 
post_id: 560
created: 2009/04/27 19:05:07
created_gmt: 2009/04/27 11:05:07
comment_status: open
post_name: %e5%8e%9f%e5%88%9b%e5%ae%9e%e6%b5%8bmysql%e6%97%b6%e9%97%b4%e5%ad%97%e6%ae%b5%e7%a9%b6%e7%ab%9f%e4%bd%bf%e7%94%a8int%e8%bf%98%e6%98%afdatetime%ef%bc%9f
status: publish
post_type: post

# [原创][实测]MySQL时间字段究竟使用INT还是DateTime？

[ 文章作者：陈臻 本文版本：v1.0 最后修改：2009.4.27 转载请注明原文链接：[http://www.54chen.com/c/560](../c/560) ] 环境： Windows XP [PHP ](/c/441)Version 5.2.9 MySQL Server 5.1 第一步、创建一个表date_test（非定长、int时间） CREATE TABLE `test`.`date_test` ( `id` INT NOT NULL AUTO_INCREMENT , `start_time` INT NOT NULL , `some_content` VARCHAR( 255 ) NOT NULL , PRIMARY KEY ( `id` ) ) ENGINE = InnoDB; 第二步、创建第二个表date_test2（定长、int时间） CREATE TABLE `test`.`date_test2` ( `id` INT NOT NULL AUTO_INCREMENT , `start_time` INT NOT NULL , `some_content` CHAR( 255 ) NOT NULL , PRIMARY KEY ( `id` ) ) ENGINE = InnoDB; 第三步、创建第三个[表](http://www.54chen.com/c/539)date_test3（varchar、datetime时间） CREATE TABLE `test`.`date_test3` ( `id` INT NOT NULL AUTO_INCREMENT , `start_time` DATETIME NOT NULL , `some_content` VARCHAR( 255 ) NOT NULL , PRIMARY KEY ( `id` ) ) ENGINE = InnoDB; 第四步、创建第四个表date_test3（char、datetime时间） CREATE TABLE `test`.`date_test4` ( `id` INT NOT NULL AUTO_INCREMENT , `start_time` DATETIME NOT NULL , `some_content` CHAR( 255 ) NOT NULL , PRIMARY KEY ( `id` ) ) ENGINE = InnoDB; ok，现在我们开始做[测试](http://www.54chen.com/c/45)，环境是php，先向各个表插入一百万条数据。插入的时候分200次，每次进库5000条。 表一执行记录：页面运行时间: 26.5997889042 秒，插入的时候发现一个有趣的现象：SELECT count( id ) FROM `date_test` WHERE 1 的结果是100w,而直接select * from `date_test`却是1,000,374条结果。（后来看到这是一个可能接近的值，请参看MySQL FAQ 3.11）。 表二执行记录：页面运行时间: 62.3908278942 秒，这次记录是1,000,066条。 表三执行记录：页面运行时间: 30.2576560974 秒，这次的是1,000,224条。 表四执行记录：页面运行时间: 67.5393900871 秒，这次的是：1,000,073条。 现在把四个表的start_time字段一一加上索引。 测试四个表的更新，分别update 100条记录，并记录时间: 表一：页面运行时间: 2.62180089951 秒（非定长，int时间） 表二：页面运行时间: 2.5475358963 秒（定长，int时间） 表三：页面运行时间: 2.45077300072 秒（varchar,datetime时间） 表四：页面运行时间: 2.82798409462 秒（char,datetime时间） 测试四个表的读取，分别select 100条随机记录，以主键id为条件查询，并记录时间： 表一：页面运行时间: 0.382651090622 秒（非定长，int时间） 表二：页面运行时间: 0.542181015015 秒（定长，int时间） 表三：页面运行时间: 0.334048032761 秒（varchar,datetime时间） 表四：页面运行时间: 0.506206989288 秒（char,datetime时间） 测试四个表的读取，分别select 10条随机记录，以star_time为条件查询，并记录时间： 表一：页面运行时间: 30.1972880363 秒（非定长，int时间） 表二：页面运行时间: 65.1926910877 秒（定长，int时间） 表三：页面运行时间: 39.7210869789 秒（varchar,datetime时间） 表四：页面运行时间: 70.4632740021 秒（char,datetime时间） 因为量比较小，所以我们默认即使是微小的变化，也是有意义的。 结论： **大数据量下，如果存在大量的select * from table where 时间>XX这样的查询，在MySQL5.1时使用int换datetime是有意义的。**

## Comments

**[乙木木](#4265 "2009-05-11 15:46:28"):** 赞一个，不过char和varchar类型的读写操作对时间消耗太大，可以说对结论的影响是很大的却没有体现在结论里

**[luobo525](#2775 "2009-04-28 09:03:34"):** 学习了~ 请问，varchar的时间比char的时间要少，这是为什么呢？

**[cc0cc](#2776 "2009-04-28 09:35:18"):** 我觉得应该是innoDB引擎默认的索引的方式，并不是以hash方式而是Btree，是否定长对速度不会有影响，不知是否还有高手有别的见解

**[cc0cc](#11842 "2009-08-19 15:08:20"):** 换了个验证码，回一下 这里where的条件是用的datetime的字段 至于varchar char的写入操作，所用时间应该相关不大

**[xCer](#11858 "2009-08-31 10:48:07"):** 大概就是楼上说的这样 我记得在MyISAM结构下，char多余的数据是以空白填充，因此该结构下尽量使用char代替varchar 而在InnoDB结构下，无论是char还是varchar，都使用了指针的概念，总是指向该串的头部，因此使用varchar往往性能比char强（因为此时varchar更节省空间）。

