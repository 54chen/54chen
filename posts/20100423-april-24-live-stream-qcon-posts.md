title: 4月24日QCon现场直播流水帖
link: http://www.54chen.com/architecture/april-24-live-stream-qcon-posts.html
author: cc0cc
description: 
post_id: 1083
created: 2010/04/23 14:40:45
created_gmt: 2010/04/23 06:40:45
comment_status: open
post_name: april-24-live-stream-qcon-posts
status: publish
post_type: post

# 4月24日QCon现场直播流水帖

才连接上3G，开始直播 Eric Gamma因为冰岛火山灰原因，没能到，更换成了Scurm的主席   1.scrum agile 30+years管理经验 存在的问题： 1.在公司和团队的环境中，存在沟通问题 2.组织的管理层没有关注到知识性的工作与体力性工作之间的差异 3.高技术的工作人员存在优先选择的问题 4.人们觉得我们不了解我们工作的复杂性 agile宣言 四项价值观： 个体和交互重于过程和工具 可工作软件重于详尽的文档 。。 12个原则： 要经常性地交互可用的软件，周期从几周到几个月不等到，交互的时间越短越好（经常和客户沟通） 最重要的一点：激励项目人员，调动工作经验，钱不是万能的 非常有意思的原则 面对面的交谈 可用的软件是衡量进度的主要指标 项目周期需要保持长期稳定，要有节奏，不要过多催促 技术的精益求精及对设计的不断完善将提升敏捷性 尽最大可能简洁 允许团队自己放手去做，相信你的团队成员，他们会犯错误但是可以理解的 团队经常地自我反省如何提高技术能力 Scrum 不仅仅用在软件上，其他用途也是可以的 Scrum的角色职能 ScrumMaster的误解：这个角色不是项目经理，是为这个团队提供服务的。5-12个人组织团队 产品的backlog：产品功能表 任务表:sprint backlog 在实现上述的功能，我们要做些什么，这并不是真正意义上的时间表 sprint planning:30天要完成这个工作 日常会议 sprint review sprint retrospective 是一种风险管理的方式，把风险降到最低，特别是大规模开发某个程序的话，可以规避这种风险 可以经常性问客户这样行不行 它是一个非常科学的方式 三个原则能解决很多问题： 询问团队人员 检查代码、检查工作程序 快速提供成绩 有许多问题会导致失败： 1.文化和不信任 2.投资人之间的交流不畅通 3.设计人员同时做多个项目 4.没有遵循sprint 5.外行人加以干涉 6.团队成员没有全力投入(我帮不了你，这不是我的事情) 7.scrum master成了项目经理 8.工作不够迅速 被误解的一些： Done:必须是可交互了 团队没有投入到改善工作中去 他们经常去探索问题而不解决 对个人的奖励而不是团队奖励 Scrum doesn't work in china 这哥们的图很牛B 中国是共产主义国家，是可以使用scrum的 文化对scrum的影响 讲一个指数，power distance index 另一个：Cultural agility index 个人主义和集体主义的文化 根据agility index 在中国是可行的 而且是更有利的执行 管理人员的指令 专业经理人官僚的作风 中国的小道消息文化 绩效的评估 避免员工与家庭分开 对成员要有耐心 **下一个是facebook的memcache实战：** [memcache@facebook](mailto:memcache@facebook) 企业的周五，穿正装 how big is facebook? 4亿用户 1000个服务器？？？错了，上千个服务器 facebook memcache规模 400M gets/s 28M sets/s 2T 收：9.7M/s 图很cool facebook terms tier：层级 cluster:数据中心 ToR:top of rack switch memcache/memcached 游戏中的memcache规则 get : on miss query data and set（这点我赞同） short profile PHP并行数据查询：php polling IO  / 有一个PHP异步IO的方案？ 用户的好友信息缓存效果并不是很好 把大数据和小数据分类 序列化与压缩 fb-serialization:快3倍 小30% gzompress serialized string mcproxy scaling servers 增加服务器 查5000个好友的时候 用UDP解决这个问题 测量时间 热门的页面：把key复制 公布热键 replicated keys 举例说在profile表里的一个字段confirmed int:Mirrored keys mutexes：锁 （这个方案用图演示的，感觉会有可能出现一次出不来信息） 公共主页的数据更新方案：多级memcache方案 删除的一致性：schema-version delete replay: delete can fail log failed deletes 用了iptable McSQL 测试： 你的动作要快，不要影响系统 python and ctypes write tests not test frameworks memcache今后的发展规划 有限的解决方案 **下一位是：twitter** **twitter的大胡子哥们** what's real-time data? 在讲什么是tweet memcached for reads 同声传译太烂了。。。 根据主键进行分区的例子 根据主键进行分区的例子和根据用户进行分区会有困难 twitter是根据时间来分区，一开始考虑不是很好的办法？这是一个非常临时性的分区方案，但也能满足99%的查询 将来的解决方式：nosql 实时数据的解决方案：适时的服务 通过offline的解决方案 发布一个新的tweet在timeline里排序，当你tweet的时候开始推送 通过提前的处理，可以提高处理量 一个表格对比08和10的tweets速度 只把数据保存到了内存里，是昂贵的 发一推给所有人的时候，尽量在一秒内，而且要保证120万的推送量 social graph: 所有数据都从内存里读，单元性地读取数据，把问题从大化小。 按照用户分解数据 数据二级索引 （看上去是复制两个维度的数据） flockDB?开源的 分布性的存储中的数据一致性 分区和索引变得越来越复杂了。。。 (时间不够了。。) 吃饭时间，下午再来。。。 回来了，在大宴会厅听阿里巴巴国际站的架构。主要是境像。 数据同步1.0 SQL复制的方式会带来数据冲突，比如说批量操作。 双站点应用拆分的问题。 CAP原理 写数据一致性和可用性 读数据容忍一致性，境像 结论：读应用更容易实现跨IDC的部署 数据同步会放大数据不一致 从设计上避免数据完整性的问题 设计业务流程的时候需要避免跨IDC的WEB Flow 阿里的读应用基本是从搜索来或者是cache来 缺陷： 双向同步的冲突 解决业务冲突 双站点扩展性已经好了很多 进入第三阶段：多站点 全境像 目标是所有机房的所有应用都必须有跨IDC的备份，数据亦然。 数据同步瓶颈分析 受制于为了满足数据一致性而对写入操作进行的排序 数据同步2.0设计原则 在数据库层面记录数据变更 并行所有可以并行的内容 有选择的侵入业务 merge操作 写应用的镜像方案： 双master在实现复杂性上会带来问题 可维护性提高 （笔记本没电了。。。） 后面还有“去哪儿”网站架构、人人网架构、新浪微博架构。。。晚上回去再补上来。。。

## Comments

**[Willko](#12256 "2010-04-24 09:37:40"):** 哥记得分析ppt

**[蜗牛](#12257 "2010-04-24 11:49:41"):** 同声传译是啥玩意？

**[xLight](#12258 "2010-04-24 12:20:21"):** "flockDB?开源的" http://github.com/twitter/flockdb

**[cfc4n](#12263 "2010-04-27 09:55:40"):** 哥,记得把人人网架构、新浪微博架构PPT传上来.你知道的.

**[cc0cc](#12265 "2010-04-28 13:32:02"):** 暂时还没得到，等有了下载的时候我通知一下

