title: resin+thrift压力测试报告
link: http://www.54chen.com/architecture/resin-thrift-benchmark.html
author: 54chen
description: 
post_id: 2284
created: 2013/02/06 15:51:18
created_gmt: 2013/02/06 07:51:18
comment_status: open
post_name: resin-thrift-benchmark
status: publish
post_type: post

# resin+thrift压力测试报告

![thrift resin](http://img04.taobaocdn.com/imgextra/i4/T1_NWcXbNXXXcQbh6a_121410.jpg) **基础条件**

位置
参数

server
resin 4 1 rose+1 thrift java version "1.6.0_29" Ubuntu 10.04 LTS 双核cpu 8G mem 

client
macbook pro ab 
**server代码** 1.用thrift创建了一个方法，内部只有几行代码： 

> logger.info("in call"); try { Thread.sleep(1000); } catch (InterruptedException e) { logger.info("sleep error"); }

2.使用的paoding-rose调用这个thrift方法。 **第一波，thrift 10 thread/sleep 1s/rose timeout 3s，ab 100并发发1000次请求** ab -n 1000 -c 100 http://10.235.7.249/test 

> Complete requests: 1000 Failed requests: 980

试了多次，一定在20次请求时开始超时，服务器端大量timeout。 **第二波，thrift 10 thread/sleep 300ms/rose timeout 3s，ab 100并发发1000次请求**

> Complete requests: 1000 Failed requests: 386

**一二波结论** 同第一波，还远未触及resin的thread上限（free版本有thread限制）。 这个值大概可以计算出来： thrift: (1s/300 ms)×10 thread = 33req/s ， resin: 3s timeout，大概能够处理进入不超时的连接为 33req/s × 3s ＝ 99 req，100个并发进来，大概会随机timeout掉1个甚至更多。 第二波花了30s左右，所以会有两三百的timeout引发的500失败出现。 

> 逆向思考： 如果需要resin的qps达到1000qps，client 1s timeout，而thrift保持300ms话，需要thrift thread: 300-400

于是得出一表格： 

resin qps
thrift client timeout
thrift return time
thrift server thread

小于1000
1s
300ms
300-400

小于1000
1s
10ms
10-20

小于100
100ms
10ms
10-20

小于2000
300ms
10ms
100-200

小于330
100ms
30ms
100

小于100
60s
3s
100
**第三波，thrift 100 thread/sleep 30ms/rose timeout 100ms，ab 100并发发1000次请求** 这次在日志中出现了非thrift timeout的异常了，而是thrift client在未关闭已经完成的处理时，新请求进来了，将会得到获取sokect失败的异常。（这种异常算是池化连接对其性能损耗，在产品工作状态下，应该优于未池化的情况。） 

> Complete requests: 1000 Failed requests: 681

**第四波，thrift 100 thread/sleep 3s/rose timeout 60s，ab 100并发发1000次请求**

> Complete requests: 1000 Failed requests: 0

因为rose timeout为60s，所以所有的等待都基本上会得到返回，当把并发加大到200也没有处理失败的情况，原因是http的默认超时应该是120s。通过netstat看server的80连接已经到了1002个。 **第五波，thrift 1000 thread/sleep 3s/rose timeout 120s，ab 4000并发发50000次请求** 这都快成了long polling server了。。。 这个状态下，load到了6，内存吃满。 

> # netstat -anop|grep :80|grep EST|wc -l 4229 # jstack 26485 | grep -c http 8001 # jstack 26485 | grep -c resin- 93 Complete requests: 50000 Failed requests: 45656 

大把大把的超时。 **综述** 1.提升thrift中逻辑处理速度，减少单个处理时间，是最有效提升thrift qps的办法。 2.thrift thread数量增加一个数量级，前端QPS提升略小于一个数量级。thrift节点数量加1，QPS提升略小于一倍，但不如加thread。 3.resin节点数量×resin qps ＝ thrift节点数量×thrift qps×网络系数，要提高resin qps，可以：提高thrift qps、增加thrift节点数量。此处网络系数肯定小于1。 4.减少不必要的网络系数影响，app+midtier的架构变为service架构，按业务内聚后再SOA化是云后台非常关键的一个原则。 5.resin4的rose框架代码表现在最多4020左右的并发，多于的将会拒绝，类似apache的max-keepalive-connections。 6.tomcat7的同一份代码表现最多在300（未经任何优化）。