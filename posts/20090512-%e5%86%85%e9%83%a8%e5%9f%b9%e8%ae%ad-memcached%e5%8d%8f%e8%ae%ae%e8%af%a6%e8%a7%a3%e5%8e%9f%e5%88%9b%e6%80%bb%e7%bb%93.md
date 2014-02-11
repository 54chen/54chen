title: 内部培训--memcached协议详解[原创][总结]
link: http://www.54chen.com/_linux_/%e5%86%85%e9%83%a8%e5%9f%b9%e8%ae%ad-memcached%e5%8d%8f%e8%ae%ae%e8%af%a6%e8%a7%a3%e5%8e%9f%e5%88%9b%e6%80%bb%e7%bb%93.html
author: cc0cc
description: 
post_id: 584
created: 2009/05/12 18:59:02
created_gmt: 2009/05/12 10:59:02
comment_status: open
post_name: %e5%86%85%e9%83%a8%e5%9f%b9%e8%ae%ad-memcached%e5%8d%8f%e8%ae%ae%e8%af%a6%e8%a7%a3%e5%8e%9f%e5%88%9b%e6%80%bb%e7%bb%93
status: publish
post_type: post

# 内部培训--memcached协议详解[原创][总结]

[ 文章作者：陈臻 本文版本：v1.0 最后修改：2009.5.12 转载请注明原文链接：<http://www.54chen.com/c/584> ] 本次讲座讲师是校内网高手张洁同学，主题是“memcached协议详解及java client分析比较”，java client分析比较线条化，只有在现场才能明白，此处只回顾memcached协议详解，为易于理解，标题段落有调整。 **一.连接** 安装好memcached，直接通过telnet localhost 11211连接。11211是memcache默认的端口。 **二.协议**

2.1store (存储)

（可用命令）set/add/replace/prepend/append <command name> <key> <flags> <exptime> <bytes> [noreply]\r\n exptime 只能精确到秒

例如： 

set name 0 0 5<回车> zhangjie<回车> STORED

区别：set可能[覆盖](/c/356)，add不能覆盖，prepend是续在前面，append是续在后面。 另：[noreply] 是新版中增加的标志，可以让服务器端不用返回。 2.2 cas（compare and set） 最新版的命令。目的保证执行的原子性。 例如： cas some 0 0 10 2 意思是：[检查](http://www.54chen.com/c/49)some的值的版本是不是2，是2才把10存进去， 2.3 get/gets

get <key>* get key1\r\n get key1 key2 key3\r\n

gets与get相比，返回[结果](/c/11)会多一个版本号：

gets some VALUE some 0 10 2 2008-03-20 END

2.4 delete（没得说的） 2.5 incr/decr（自增和自减） 2.6 stats（运行状态）

STAT curr_items 1 STAT total_items 7 STAT bytes 58 STAT curr_connections 5 STAT total_connections 6 STAT connection_structures 6 STAT cmd_get 12 STAT cmd_set 11 STAT get_hits 8 STAT get_misses 4

**三.两个java client** spymemcached   http://code.google.com/p/spymemcached Java memcached client   http://www.whalin.com/memcached 关于环状的一致性hash很有意思，有时间研究研究。

## Comments

**[agentzh](#4280 "2009-05-12 19:09:33"):** 除了协议之外，memcached 最容易被搞的是其“局部 LRU”和基于 slabs 的存储分配策略。我前不久刚在我们的 Firefox 集群中被 memcached 的不良 slabs 分配策略给搞了，哈哈！通过 -f 和 -n 选项校正，好了许多，呵呵。

**[cc0cc](#4281 "2009-05-12 19:14:39"):** 兄弟真是神速，上回你说那个用memcached做自旋锁有空写个日志学习下。

