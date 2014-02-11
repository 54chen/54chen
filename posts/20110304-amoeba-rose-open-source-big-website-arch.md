title: Amoeba+rose：开源项目搭建大型网站可扩展构架实记
link: http://www.54chen.com/java-ee/amoeba-rose-open-source-big-website-arch.html
author: 54chen
description: 
post_id: 1625
created: 2011/03/04 14:03:42
created_gmt: 2011/03/04 06:03:42
comment_status: open
post_name: amoeba-rose-open-source-big-website-arch
status: publish
post_type: post

# Amoeba+rose：开源项目搭建大型网站可扩展构架实记

![amoeba rose ](http://img04.taobaocdn.com/imgextra/i4/T1KVN2XbRpXXbyndEV_020212.jpg) java是一把双刃剑，可以很重地伤人，亦可很重地自伤。曾在一处见到，淘宝在长期使用java构建web项目后，得出一个结论：积重难返。 实际工作经验得到的结论，积重难返的原因，往往不是java本身的缘故，而是团队成员基础积累参差不齐，许多次的“一不小心”积累成了最终的结果。到了悔之晚矣的时候自然就积重难返了。如何避免java使用自伤，最关键在于，统一团队成员的code入口，框下可能发生的事情，避开不能发生的事情，言归正传，下面开始介绍两个非常不错的java开源项目，搭配两个项目，可以搞定所有的mysql+java项目的任何方向的扩展和自由的开发而不担心积重难返。 **项目简介** 1) rose框架： 来自人人网王志亮大侠的大作，其优异的性能撑下了SNS网站复杂多变的业务（人人网大量使用），同时开发非常方便，有大量的文档可供参考。 项目地址： <http://code.google.com/p/paoding-rose/> 2) Amoeba： Amoeba(变形虫)项目，专注分布式数据库 proxy 开发。对客户端透明。具有负载均衡、高可用性、sql过滤、读写分离、可路由相关的query到目标数据库、可并发请求多台数据库合并结果。使用这个项目可以将DB层透明，简单做到分库分表，甚至无限partition。作者陈思儒大侠。 下面有一篇两年前Fenng对作者的采访： <http://www.infoq.com/cn/articles/interview-chensiru-amoeba> 项目地址： <http://amoeba.meidusa.com/> **构架图** ![amoeba+rose构架图](http://img04.taobaocdn.com/imgextra/i4/T1cEt1XoXGXXX9DQkU_015423.jpg) **相关配置** 1) 配置rose中的数据源为amoeba <http://code.google.com/p/paoding-rose/wiki/Jade_DataSource> 2) 配置amoeba为mysql(使用amoeba-mysql) <http://amoeba.meidusa.com/amoeba.pdf>

## Comments

**[Timo](#13381 "2011-03-04 14:12:08"):** Amoeba应该也是阿里系的同学开发的吧。只是没看到太多实践和运维资料

**[janwen](#13382 "2011-03-04 14:32:52"):** 看图感觉真的好简单，可惜一直没机会做分布式项目

**[chris](#13387 "2011-03-06 21:23:45"):** 准确的说是一个曾经在阿里的同学在阿里期间开发的。呵呵。

