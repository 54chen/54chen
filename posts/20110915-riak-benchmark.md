title: riak源码阅读手记 压力测试
link: http://www.54chen.com/document/riak-benchmark.html
author: 54chen
description: 
post_id: 1824
created: 2011/09/15 15:54:41
created_gmt: 2011/09/15 07:54:41
comment_status: open
post_name: riak-benchmark
status: publish
post_type: post

# riak源码阅读手记 压力测试

![erlang riak](http://wiki.basho.com/images/logo_wiki.png) **基础环境：** 三个台式机 **一次操作：** 操作一段购物车数据，先写包括3个货物的数据，然后取出来，再加上三个后存入。 **压力结果：** 1) 10个线程，无连接池化，每次新连接。PBClient。每次req包括上述一次操作里的一写一读一写三次。 Rate: 122 req/s Rate: 103 req/s Rate: 119 req/s Rate: 110 req/s Rate: 117 req/s Rate: 116 req/s Rate: 121 req/s Rate: 110 req/s Rate: 116 req/s Rate: 111 req/s Rate: 123 req/s Rate: 122 req/s Rate: 119 req/s Rate: 123 req/s Rate: 110 req/s 三个节点平均load为小于1。 2）50个线程，无连接池化，每次新连接。PBClient。每次req包括上述一次操作里的一写一读一写三次。 Rate: 124 req/s Rate: 119 req/s Rate: 124 req/s Rate: 127 req/s Rate: 114 req/s Rate: 103 req/s Rate: 93 req/s Rate: 110 req/s Rate: 120 req/s Rate: 117 req/s Rate: 121 req/s Rate: 119 req/s Rate: 109 req/s 三个节点平均load为1。接入节点load略高。 3）100个线程，无连接池化，每次新连接。PBClient。每次req包括上述一次操作里的一写一读一写三次。 Rate: 89 req/s Rate: 109 req/s Rate: 97 req/s Rate: 108 req/s Rate: 105 req/s Rate: 105 req/s Rate: 105 req/s Rate: 106 req/s Rate: 104 req/s Rate: 106 req/s Rate: 103 req/s Rate: 91 req/s 三个节点平均load为1。接入节点load为大于2。 以上数据为从一个节点打入数据，接入节点负载略高。无池化，在建立连接浪费可能比较多。进行改进，合并发起连接的过程和接入节变成所有节点。 1）三个节点共同接入，最大连接数150，相当于池化连接，150个线程，PBClient。 Rate: 271 req/s Rate: 275 req/s Rate: 256 req/s Rate: 276 req/s Rate: 287 req/s Rate: 288 req/s Rate: 280 req/s Rate: 293 req/s Rate: 279 req/s Rate: 291 req/s Rate: 254 req/s Rate: 287 req/s Rate: 288 req/s Rate: 283 req/s Rate: 292 req/s 平均load 1，无明显负载偏移。 2）三个节点共同接入，最大连接数300，相当于池化连接，300个线程，PBClient。 Rate: 273 req/s Rate: 234 req/s Rate: 264 req/s Rate: 264 req/s Rate: 263 req/s Rate: 277 req/s Rate: 253 req/s Rate: 250 req/s Rate: 258 req/s Rate: 218 req/s Rate: 237 req/s Rate: 268 req/s Rate: 232 req/s Rate: 245 req/s Rate: 247 req/s 平均load 1.5，无明显负载偏移。 同机器的mysql性能压测结论为：http://www.54chen.com/java-ee/amoeba-benchmark-report.html 可得到比mysql qps高3倍以上（因为riak的压测中每req为2写1读）。

## Comments

**[sosan](#14925 "2012-06-20 23:06:37"):** 看了你的测试结果，自己用单机测试了下Riak性能，感觉和Mongodb比起来性能有差距，看了joyent的压测报告说加机器可以提高性能，不知是否是这样，机器越多性能越高？我在16G内存双核的服务器上单线程做测试（默认配置），插入都要3-5ms

**[54chen](#14928 "2012-06-21 10:16:18"):** mongodb的sharding策略和riak各有千秋，riak是完整的dynamo类系统，提速只需要加机器，主要优点是cluster的性能稳定。

**[scott](#13819 "2011-09-23 09:50:39"):** 你这个压力测试都是用什么做的压力测试？ 恕我眼拙，你的测试看不出来这个产品和其他的NoSQL解决方案的差距，好？坏？ 在Linux环境下，单机，8G内存，4核（4×2.2）CPU，用RoadRunner压Nginx写Oracle能达到700-800请求每秒（插入数据，64个字符）。 用RoadRunner压Nginx写Memcached能达到12000请求每秒（插入数据，64个字符）。

**[54chen](#13820 "2011-09-23 10:21:15"):** 这是在虚拟机进行的压测，文章末尾有同等条件下mysql的压测情况，硬件提升的话，应该是同等提升的。

