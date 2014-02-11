title: 7月3日 infoQ Qclub分享演示文稿下载
link: http://www.54chen.com/architecture/infoq-qclub-share-presentation-download.html
author: 54chen
description: 
post_id: 1227
created: 2010/07/06 15:49:50
created_gmt: 2010/07/06 07:49:50
comment_status: open
post_name: infoq-qclub-share-presentation-download
status: publish
post_type: post

# 7月3日 infoQ Qclub分享演示文稿下载

本次活动以RIA为主题，参会者多为关心RIA及后端数据服务的开发者和管理者。 我的主题是："Ria的强力后盾：rest+海量存储 主要内容是给参会者分享了淘宝在nginx上作的关于rest的事情，和一些人人网nuclear的原理。 下面是此次会上我所使用的slide。（国外 有点慢 需忍） 

**[Ria的强力后盾：rest+海量存储 ](http://www.slideshare.net/54chen/riarest-4691046)**

View more [presentations](http://www.slideshare.net/) from [zhen chen](http://www.slideshare.net/54chen).

## Comments

**[kafka0102](#12484 "2010-07-06 20:05:17"):** 我在之前你的一篇文章里也看到你对CAP的理解，从我对那篇论文及一些文章中来看，你的理解似乎有偏差，这里也算个讨论和交流。CAP里的partition tolerance应该不是我们常说的存储系统的分区能力（可扩展性方面），而是网络分区，这个网络分区广义上可以认为是系统内的两台机器不能正常通信（一些文章提到是两个网络间不能通信，比如两个机房，这限制了这一理解）。所以，因为partition tolerance的存在，强一致性和可用性只能取其一。在网络分区情况下，要强一致性，则请求的响应就不能正常返回（失掉可用性）；如果要可用性，则返回的数据可能为脏（失掉强一致性）。

**[54chen](#12485 "2010-07-06 20:57:57"):** 可能在写的时候这两个词有些容易混淆了，一个是分区容错性，一个是数据分区，不是一个东西，数据分区只算是针对网络分区的容错性所提出的一个小策略的一部分而已 另外partition tolerance的本意的确是针对容错的情况，但我觉得除了节点故障这样的情况外，增加上新的节点也是同样的需要容错的，所以最直接的感受就是容易增加删除节点，只是方便理解CAP而已，不然NRW里的复制也可以作为容错性的因素之一，这样讲解CAP的时候可能目的性就不是这么强了。

**[groovyq](#12577 "2010-08-07 12:10:14"):** 这个演讲视频将于8月13日在InfoQ中文站上发布，届时可以一睹楼主的芳容！：）我就先睹为快了，讲的不错！

**[54chen](#12628 "2010-08-09 11:04:23"):** 哥是如何得知这个内部消息的，难道是消息灵通人仕

