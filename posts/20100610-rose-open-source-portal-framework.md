title: 一场技术的圣战：rose开源框架之portal
link: http://www.54chen.com/architecture/rose-open-source-portal-framework.html
author: 54chen
description: 
post_id: 1204
created: 2010/06/10 12:26:11
created_gmt: 2010/06/10 04:26:11
comment_status: open
post_name: rose-open-source-portal-framework
status: publish
post_type: post

# 一场技术的圣战：rose开源框架之portal

作者：人人网架构师 王志亮

> **（54chen按：此文为客座博文，王志亮大侠，人人网架构师，疱丁分词创始人，rose是他的另一开源大作。关于69圣战，请看[http://zhidao.baidu.com/question/158643718.html?si=2&wtp=wk](http://zhidao.baidu.com/question/158643718.html?si=2&wtp=wk)）**

2010年的6月9日是一个圣战的日子，零点一到就有人开始，好戏也如约在晚上7点发生。人人网战场是SJ的公共主页：<http://page.renren.com/sj>

 

对不同人，这个日子意味着不同，滋味也不同。作为人人网技术团队，我们要保证服务能力、用户体验能够应付得了这个挑战。

 

某一个服务器的能力总有限，为了应付突然增长的读写量，web服务架构、内部服务架构、数据库架构等要能够轻松通过服务器调配来满足。就web服务器而言，我们增加了1倍的机器。现在再回头来看监控的数据，一切显得美好。这个期间整个服务做到了服务能力没有中断。除此之外，在这次圣战中，其中还有一项我们**独有的**技术起到了重要的作用：[rose portal](http://code.google.com/p/paoding-rose/) ，下面作一个介绍：

 

图1是sj的主页：

 

![](http://img05.taobaocdn.com/bao/uploaded/i5/T1RDFCXcdeXXcmuYvb_123212.jpg) 图1 sj在人人网的公共主页 

这个页面分为三列：

  * 左边有 “推荐给好友”、“基本信息”、“相册”;
  * 中间有“给SJ留言”、“好友留言”;
  * 右边有“好友”，“人人的用户还关注”等。

在后台，这些被分解为不同的模块，我们称之为”window”。这每一个window都意味着可能连接一个独立的服务集群，比如基本信息服务、留言服务、好友服务、相册服务等等。这样，一个公共主页就等于多个独立的、可配置的window模块组成，如图2所示 ：

![](http://img07.taobaocdn.com/bao/uploaded/i7/T1M_hCXiplXXXZgsbb_123302.jpg)

图2 公共主页的window模块组成

随着伟大圣战的深入，这个页面就变成这样(右边的栏目不见了)，如图3所示：

![](http://img06.taobaocdn.com/bao/uploaded/i6/T1WmpCXntyXXcpbsDb_123341.jpg)

图3 圣战进行中时模块的自动保护

产品同学看到此情此景，仍然很开心：“只要留言的window能在，其它的没在不要紧”

但是不一会，继续恶化，如图4所示：

![](http://img06.taobaocdn.com/bao/uploaded/i6/T1qCJCXaRuXXXtnc6b_123408.jpg)

## Comments

**[nalimai](#12483 "2010-07-05 21:19:19"):** 一场技术的圣战：rose开源框架之portal

