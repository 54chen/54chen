title: 校内UGC技术讲座：postgreSQL Search slide共享
link: http://www.54chen.com/architecture/ugc-technical-seminars-in-schools-postgresql-search-slide-sharing.html
author: cc0cc
description: 
post_id: 683
created: 2009/07/31 15:07:20
created_gmt: 2009/07/31 07:07:20
comment_status: open
post_name: ugc-technical-seminars-in-schools-postgresql-search-slide-sharing
status: publish
post_type: post

# 校内UGC技术讲座：postgreSQL Search slide共享

[PostgreSQL Search](http://www.slideshare.net/guestb75c3a/postgresql-search)

提前放出这次Tech Talk的slide，show给所有人。讲述的主题是[postgreSQL](/601-%e5%8e%9f%e5%88%9b%e4%bd%bf%e7%94%a8postgresql%e6%90%ad%e5%bb%ba%e6%af%94lucene%e6%96%b9%e4%be%bfn%e5%80%8d%e7%9a%84%e5%85%a8%e6%96%87%e6%90%9c%e7%b4%a2-%e7%ac%ac%e4%b8%80%e9%83%a8%e5%88%86/)的基础和利用bamboo做全文检索的方法。

## Comments

**[lphy](#10712 "2009-08-03 15:31:20"):** 你好 postgreSQL+bamboo与sphinx与luncene对比各有什么优缺点？

**[cc0cc](#10716 "2009-08-03 16:47:53"):** postgreSQL+bamboo在即时性要求很高的环境适用，yahoo关系里搜索好友和推荐好友都是用这个做的，还有yahoo的一些类似股票等的搜索，都是用的pgsql，sphinx和lucene最大的缺点就是索引更新慢。

**[lphy](#10719 "2009-08-03 18:49:42"):** 谢谢指点！对于大段文章的中文全文搜索postgreSQL+bamboo效率和效果怎么样？

**[cc0cc](#10720 "2009-08-03 20:23:47"):** 全文索引是pgsql的强项，一般来说，单个server在百万级数量过千的QPS不是问题，再在前端用上memcache的话，一般的大中型系统都适用

**[lphy](#10733 "2009-08-04 11:38:24"):** pgsql同步mysql的数据有没有什么好的方法？现有几个G的mysql想同步过来做全文搜索。

**[cc0cc](#10736 "2009-08-04 13:52:41"):** 有两种办法：1.修改你系统的代码，在增删改的时候对pg进行操作；2.定时从mysql读数据到一个没有任何索引的pgsql表里，最后再加上索引（或者用\copy from xx \copy to xx的指令，效率是非常高的）

