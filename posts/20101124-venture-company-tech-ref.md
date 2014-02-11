title: 创业公司技术选型参考
link: http://www.54chen.com/architecture/venture-company-tech-ref.html
author: 54chen
description: 
post_id: 1467
created: 2010/11/24 14:17:12
created_gmt: 2010/11/24 06:17:12
comment_status: open
post_name: venture-company-tech-ref
status: publish
post_type: post

# 创业公司技术选型参考

![54chen-创业公司选择技术](http://img03.taobaocdn.com/imgextra/i3/T1.1RSXmRfXXXjG9sV_021627.jpg) **java推荐框架** web项目来说，spring、struts是必选，当然有更加好用的，推荐来自疱丁分词作者王志亮在人人网的rose框架，使用上手快，配置少，是创业公司java必备。 **php框架推荐** zend framework，或者直接写个简单的框架，php的框架更加倾向去规范代码，让所有项目在新人加入时快速上手。 **代码版本控制** subversion是必选工具，简单易学，git也开始流行，也是可选方案。 **jar包依赖管理** 这是针对java项目，还在使用ant的朋友，可以考虑换换了，特别的，如果你的公司在很快扩张的时候，这个选择能让未来避开依赖混乱，遇事集体更新困难的困境。 **公共代码建立** 长期可遇见的公共部分，比如用户信息获取，memcache管理，毋庸置疑地需要提供公共的方法，越早越好。 **代码可扩展** 这就考量上面选择框架的气候是不是合理了，这里可扩展是指，在负载越来越大的时候，要能很轻易配置读写分离，rose在这方面做得很优雅，只需要简单配置就梦把看的代码用上新的数据源。 **code review** 有许多好用的系统，比如Review Board等，让参与者都知道修改，并且在最早期发现问题。 **bug系统** jira、Bugfree等等，用系统控制流程。 **培训体系** 技术需要交流才会有进步，团体的进步才是真的进步。所以尽早建立起内部的培训体系非常有必要，同时也是活跃团体气氛的很好方法，其频度控制在两周一次最好。 **知识管理** php+mysql的mediawiki是首选，每个优秀的工程师，都必须首先是一个完全熟悉wiki语法擅长写wiki的工程师。早年搞了一个把mysql换成postgresql的方案见　<http://www.54chen.com/php-tech/mediawiki-pgsql.html> **项目管理** 从项目初期的demo设计>产品设计>技术架构>技术方案>技术实施>测试>理程碑>上线，每一环都需要详细控制，实施Scrum或者是Scrum变体，都是不错的方案。任由团队想到哪里做到哪里的结果是无法预估商业产品的出炉。 **流量监控** 流量监控比较出名的有Cacti、ganglia，安装上ganglia要复杂一点点，尽量用yum安装，ganglia的集群和数据功能相对cacti要强大一点。 **服务器操作系统** daxuxu同学推荐debian，不过我的工作环境基本都是centOS，用习惯了一个很难改变，个人感觉debian系统的环境相对复杂一些。

## Comments

**[xiaog](#13221 "2010-11-24 17:45:11"):** ant + ivy 一样管理依赖，强大的spring就是这么干的。

**[janwen](#13257 "2010-12-11 10:35:20"):** java领域的Hibernate等ormapping框架互联网公司都不用了？麻烦了，得研究点别的了

**[lanye](#13222 "2010-11-24 18:01:15"):** 崇拜

**[阿政](#13223 "2010-11-25 13:00:20"):** 很好 好好参考一下 :-) 不过我们现在代码版本控制都是用的HG jar包依赖管理 可以用MAVEN 用了好久了一直感觉不错

**[daxuxu](#13224 "2010-11-25 15:47:20"):** 技术选型其实还可以加一个操作系统的：推荐debian，系统安装快速，内核升级方便，安装一些监控工具也很快速，包管理器强大（超级牛力的apt-get）。

**[54chen](#13225 "2010-11-25 18:48:56"):** very good

**[wensheng](#13227 "2010-11-26 11:11:26"):** 一直使用CentOS，发现玩会儿ubuntu都不上手了，debian做服务器的稳定性有待商榷。

