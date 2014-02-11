title: MySQL的性能调优工具：比mysqlreport更方便的tuning-primer.sh
link: http://www.54chen.com/php-tech/mysql%e7%9a%84%e6%80%a7%e8%83%bd%e8%b0%83%e4%bc%98%e5%b7%a5%e5%85%b7%ef%bc%9a%e6%af%94mysqlreport%e6%9b%b4%e6%96%b9%e4%be%bf%e7%9a%84tuning-primersh.html
author: cc0cc
description: 
post_id: 358
created: 2008/12/22 09:43:08
created_gmt: 2008/12/22 01:43:08
comment_status: open
post_name: mysql%e7%9a%84%e6%80%a7%e8%83%bd%e8%b0%83%e4%bc%98%e5%b7%a5%e5%85%b7%ef%bc%9a%e6%af%94mysqlreport%e6%9b%b4%e6%96%b9%e4%be%bf%e7%9a%84tuning-primersh
status: publish
post_type: post

# MySQL的性能调优工具：比mysqlreport更方便的tuning-primer.sh

Sundry MySQL提供的脚本相比mysqlreport更进一步：除了报表还进一步提供了修改建议。安装和使用非常简单：

> wget <http://www.day32.com/MySQL/tuning-primer.sh> chmod +x tuning-primer.sh ./tuning-primer.sh

和mysqlreport一样，tuning-primer.sh也支持.my.cnf 

> [client] user = USERNAME password = PASSWORD socket = /tmp/mysql.sock

 

样例输出：在终端上按照问题重要程度分别用黄色/红色字符标记问题

> \-- MYSQL PERFORMANCE TUNING PRIMER -- \- By: Matthew Montgomery - 
> 
>  
> 
> MySQL Version 5.0.45 i686
> 
> Uptime = 19 days 8 hrs 32 min 54 sec Avg. qps = 0 Total Questions = 264260 Threads Connected = 1 
> 
> Server has been running for over 48hrs. It should be safe to follow these recommendations 
> 
> To find out more information on how each of these runtime variables effects performance visit: http://dev.mysql.com/doc/refman/5.0/en/server-system-variables.html Visit http://www.mysql.com/products/enterprise/advisors.html for info about MySQL's Enterprise Monitoring and Advisory Service 
> 
> SLOW QUERIES The slow query log is NOT enabled. Current long_query_time = 10 sec. You have 0 out of 264274 that take longer than 10 sec. to complete Your long_query_time may be too high, I typically set this under 5 sec. 
> 
> BINARY UPDATE LOG The binary update log is NOT enabled. You will not be able to do point in time recovery See http://dev.mysql.com/doc/refman/5.0/en/point-in-time-recovery.html 
> 
> WORKER THREADS Current thread_cache_size = 0 Current threads_cached = 0 Current threads_per_sec = 1 Historic threads_per_sec = 0 Your thread_cache_size is fine 
> 
> MAX CONNECTIONS Current max_connections = 100 Current threads_connected = 1 Historic max_used_connections = 33 The number of used connections is 33% of the configured maximum. Your max_connections variable seems to be fine. 
> 
> MEMORY USAGE Max Memory Ever Allocated : 96 M Configured Max Per-thread Buffers : 268 M Configured Max Global Buffers : 7 M Configured Max Memory Limit : 276 M Physical Memory : 1.97 G Max memory limit seem to be within acceptable norms 
> 
> KEY BUFFER Current MyISAM index space = 8 M Current key_buffer_size = 7 M Key cache miss rate is 1 : 1817 Key buffer fill ratio = 6.00 % Your key_buffer_size seems to be too high. Perhaps you can use these resources elsewhere 
> 
> QUERY CACHE Query cache is supported but not enabled Perhaps you should set the query_cache_size 
> 
> SORT OPERATIONS Current sort_buffer_size = 2 M Current read_rnd_buffer_size = 256 K Sort buffer seems to be fine 
> 
> JOINS Current join_buffer_size = 132.00 K You have had 0 queries where a join could not use an index properly Your joins seem to be using indexes properly 
> 
> OPEN FILES LIMIT Current open_files_limit = 1024 files The open_files_limit should typically be set to at least 2x-3x that of table_cache if you have heavy MyISAM usage. Your open_files_limit value seems to be fine 
> 
> TABLE CACHE Current table_cache value = 64 tables You have a total of 125 tables You have 64 open tables. Current table_cache hit rate is 9%, while 100% of your table cache is in use **You should probably increase your table_cache ** TEMP TABLES Current max_heap_table_size = 16 M Current tmp_table_size = 32 M Of 564 temp tables, 6% were created on disk Effective in-memory tmp_table_size is limited to max_heap_table_size. Created disk tmp tables ratio seems fine 
> 
> TABLE SCANS Current read_buffer_size = 128 K Current table scan ratio = 1 : 1 read_buffer_size seems to be fine 
> 
> TABLE LOCKING Current Lock Wait ratio = 0 : 264392 Your table locking seems to be fine

 

更有用是作者总结的处理MySQL性能问题处理的优先级：尤其是头3条，基本上可以解决大部分瓶颈问题的原因。 # Slow Query Log 慢查询 尤其是like操作，性能杀手，轻易不要使用，让全文索引交给[Lucene](http://www.chedong.com/tech/lucene.html)或者利用Tag机制减少like操作； # Max Connections 并发连接数：一个MySQL deamon缺省最大连接数是100，调到更高只是为了出现问题是给我们更多的缓冲时间而不是任其一直处于那么高的状态，并发连接数类似于等候大厅：当等候人数过多的时候，一味扩大等候厅不是根本解决问题的办法，提高业务的处理速度，多开几个窗口才是更好的解决方法；我的经验就是[超过100： 数据就要想办法（镜像或者分片）分布到更多Deamon上](http://www.chedong.com/blog/archives/000945.html)； # Worker Threads： [Jeremy Zawondy 曾在部落格上說到：Thread caching](http://jeremy.zawodny.com/blog/archives/000173.html) 並不是我們最需要關心的問題，但當你解決了所有其他更嚴重的問題之後，它就會是最嚴重的問題。(thread caching really wasn't the worst of our problems. But it became the worst after we had fixed all the bigger ones.) # Key Buffer  # Query Cache  # Sort Buffer  # Joins  # Temp Tables 临时表 # Table (Open & Definition) Cache 表缓存； # Table Locking 表锁定 # Table Scans (read_buffer)  # Innodb Status 

其他一些工具：  1 [mytop](http://jeremy.zawodny.com/mysql/mytop/)： 一个top like的show processlist; 2 使用cacti做MySQL的监控：推荐[配置模板](http://code.google.com/p/mysql-cacti-templates/)； 3 把binlog导出成文本和slowquery的格式几乎是一样的，调用mysqlslowquery脚本分析，有时候也会有意外收获； 

谢谢 oldplantegg 补充：

## Comments

**[根](#98 "2008-12-24 16:41:08"):** 很好用，，收了！

