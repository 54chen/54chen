title: 移动互联网api设计实践
link: http://www.54chen.com/web-ral/mobile-net-api-design.html
author: 54chen
description: 
post_id: 1916
created: 2011/12/05 17:42:02
created_gmt: 2011/12/05 09:42:02
comment_status: open
post_name: mobile-net-api-design
status: publish
post_type: post

# 移动互联网api设计实践

![api quota perf design](http://img02.taobaocdn.com/imgextra/i2/13078490/T28EicXgtXXXXXXXXX_!!13078490.jpg) 过去一年时间里，都在做中国移动互联网下的API设计工作，所面对的情况可能是最具中国特色的了。如果你在尝试构架一整套的新的API供你的客户端使用，可以尝试以下的几点： **1）api所使用协议，要能够不怕被加js、加广告和各种代码。** 全国各地的运营商，有习惯挂广告、加代码等等，要做的事情就是在客户端做各种解析判断。实践证明，这帮孙子的设备是检测http协议的head头里的content-type，如果是html/text则会加，如果是text/json就不加了，所以，你懂的。 **2）api可视化** 你的app上架了，后端也布好了。然后呢，就天天看下载量。但后端好坏一问三不知。所以在设计api时，要提前回答以下问题： 1.此时此刻，有多少个app正在调用这些api，每分钟多少个？ 2.他们（api）有快？ 3.能否很简单地通过浏览器快速debug？ 4.能否快速禁止单个用户？ **3）链路** 在幅员辽阔的我国，总是一个地方到一个地方的IDC直接就ping不通（移动互联网下更甚）。于是需要花钱自己打通这些地方，没有专线，只有代理再代理。 **4）api quota与perfcounter** 对api进行quota限制，针对每个api每个人，都有限额。 这个限制的传统做法： 1.每天的上限调用次数。每天半夜清空，一天一个量累计。 2.每分钟的频度。这个防止有恶性的突发情况。 上述二者，缺一不可。 perfcounter用于对api的监控设计指标，如果一个api有异常，应当十分灵敏地得出结论，而不是误报连连。

## Comments

**[nodexy](#14075 "2011-12-06 14:42:54"):** 确实有我国特色，呵，很不错的经验之谈

**[askJson](#14076 "2011-12-06 14:58:52"):** The MIME media type for JSON text is application/json.

**[xLight](#14077 "2011-12-06 16:23:10"):** 的确很具有中国特色啊

