title: riak源码阅读手记一 初出茅庐 项目入口
link: http://www.54chen.com/document/riak-source.html
author: 54chen
description: 
post_id: 1796
created: 2011/08/22 20:28:13
created_gmt: 2011/08/22 12:28:13
comment_status: open
post_name: riak-source
status: publish
post_type: post

# riak源码阅读手记一 初出茅庐 项目入口

![riak](http://wiki.basho.com/images/logo_wiki.png) **简介** basho（相扑）是一家美国的技术公司，专营数据存储和管理软件，11年6月30日获得了750万美元的融资。basho将riak开源，通过收取riak的维护和管理界面软件的使用费用来赚钱，和resin的公司类似。 今天主要研究的是，basho旗下的riak:一个类dynamo系统的kv存储。riak使用了erlang进行开发，将代码精简到极致。 **rebar** riak的代码使用了一个叫做rebar（钢筋）的erlang构建工具，使用起来真的很方便，其使用方法建议参考这篇文章：http://www.linezing.com/blog/?p=347。每个项目下，都有一个叫做rebar.config的文件，里面用erlang语法记录了这个项目需要的目录、环境、版本、以及依赖的包在github上的位置。真的很方便。riak项目中，rebar和reltool被大量使用。reltool使用方法参见 http://erlangdisplay.iteye.com/blog/508944 riak的总控工程：https://github.com/basho/riak。 看rebar.config定义的依赖部分: 

> {deps, [ {cluster_info, "1.1.*", {git, "git://github.com/basho/cluster_info", {branch, "master"}}}, %% 这个模块提供了一个松散易扩展的导出集群节点状态的办法。可以收集的信息包括：时间、所有的erlang进程的静态统计、网络连接情况、cpu和内存、大队列进程、内部内存调用统计、ETS、各节点的名字版本等 {luwak, "1.*", {git, "git://github.com/basho/luwak", {branch, "master"}}}, %% 大对象存储。用来存音频视频啥的。 {riak_kv, "0.14.*", {git, "git://github.com/basho/riak_kv", {branch, "master"}}}, %% 这个模块依赖riak_core，在这一层里，封装了给各种client调用的接口，处理具体存储、M/R等。 {riak_search, "0.14.*", {git, "git://github.com/basho/riak_search", {branch, "master"}}} %% 基于riak的全文检索实现 ]}.

**构建过程** 通过rebar的reltool支持，在riak项目中，rebar.conf中定义了{sub_dirs,["rel"]}，表示在rel目录下使用reltool创建一个发行版本。 在rel目录的rel.config中，{lib_dirs, ["../deps"]}，定义了所有的子模块都放在了deps目录下。很长的一个config文件，有点像ant脚本。 **函数规范定义：spec** -spec Module:Function(ArgType1, ..., ArgTypeN) -> ReturnType. 函数的参数数目必须与函数规范定义相同，否则编译出错。定义了type及spec，我们可以使用 dialyzer 对代码进行静态分析，在运行之前发现很多低级或者隐藏的错误。