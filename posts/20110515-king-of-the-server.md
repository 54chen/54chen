title: 硬件为王，软件为帝--记突增的访问
link: http://www.54chen.com/architecture/king-of-the-server.html
author: 54chen
description: 
post_id: 1699
created: 2011/05/15 00:22:07
created_gmt: 2011/05/14 16:22:07
comment_status: open
post_name: king-of-the-server
status: publish
post_type: post

# 硬件为王，软件为帝--记突增的访问

![mysql](http://img03.taobaocdn.com/imgextra/i3/T1pM1bXgdlXXapI9ra_122249.jpg) 五一归来两周，精力都放在了因为服务器压力突增而来的各种鸡飞狗跳的事情上，今天总结一下，算是对过去两周的总结，希望对各创业团队有所借鉴。 1.访问突增，连锁反应 最先开始出现问题的，是在小负载下没有出现的问题，在大压力时，只要DB一堵，表像就会是循环锁死。 解决之道： 服务要有清晰的架构，开发规范里一定要有明确的架构层次规范，最好还有代码的层次规范。 2.mysql设计不合理，压力指数增加 mysql中存在大量的更新操作，特别是大字段或者变长字段的频繁更新，导致磁盘io居高不下，慢查询越来越多。 硬解决之道： raid卡加电，开启raid写缓存：一般服务器的raid卡都没有开启，开启是有一定用处的，但有一个前提，不是待续的高写入，否则这个写缓存是没有意义的。电池充电时无法缓存，一般三个月充一次电。 转微博：FusionioChina 回复 @YauzZ:多一个故障点。多一份丢数据的可能。而且目前有Raid卡的内存做到1GB。丢不起呀。(5月11日 23:29) 从raid分到多个盘、盘柜，都是从硬件上用多硬盘分担的好方案。 软解决之道： 写方案建立mysql隔离层，在写入前预先处理，类似软件缓存方案，效果明显，修改复杂。 3.后续可做 灵活的中间层数据层切换逻辑，让负载可以随时转移、分隔，基本可以达到“人傻钱多，万事不愁”。