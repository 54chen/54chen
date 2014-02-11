title: [java]用httpclient做压力测试时Too many open files的解决办法
link: http://www.54chen.com/java-ee/java-httpclient-too-many-open-files.html
author: 54chen
description: 
post_id: 1563
created: 2011/01/10 20:33:27
created_gmt: 2011/01/10 12:33:27
comment_status: open
post_name: java-httpclient-too-many-open-files
status: publish
post_type: post

# [java]用httpclient做压力测试时Too many open files的解决办法

![压力测试httpclient](http://hc.apache.org/images/logos/httpcomponents.png) 在工作过程中，用httpclient去压测一个web api，发现压一小段时间就出现了Too many open files。 实际上，HttpClient建立Socket时 ，post.releaseConnection()并没有真正关闭连接，而是将该连接提交给 MultiThreadedHttpConnectionManager，等待复用。 而http的连接是等待timeout才会自动断开的，所以，当用完系统的句柄后，自然会报Too many open files。 **解决办法：** 设置post方法的header，增加 

> `post.addRequestHeader( "Connection", "close"); client.getParams().setBooleanParameter( "http.protocol.expect-continue" , false ); `

## Comments

**[nodexy](#14898 "2012-05-17 17:26:52"):** 开启系统最大句柄数是前提，但就算到了65535，我们经常并不按1s去算，而是1分钟甚至10分钟，然后计算平均1s的量；所以connection复用很重要，LZ的point就在用完即关！ PS: tsung - 压测好帮手 :) 不过自己用HtppClient写就可以更灵活地控制req和resp .

**[Sutra](#13324 "2011-01-13 23:56:42"):** sysctl -w kern.maxfiles=xxxxxxxx

**[zava](#13314 "2011-01-11 13:07:35"):** 是否是客户端（httpClient端）出现了句柄过多的形式，如果是这样，能不能设置ConnectionManager的connection数目。 不过既然是性能测试，那对客户端的并发数作限制也不太好。

