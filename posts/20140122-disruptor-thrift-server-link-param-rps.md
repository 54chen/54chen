title: disruptor thrift server连接参数与rps数值影响记录
link: http://www.54chen.com/java-ee/disruptor-thrift-server-link-param-rps.html
author: 54chen
description: 
post_id: 2501
created: 2014/01/22 20:51:21
created_gmt: 2014/01/22 12:51:21
comment_status: open
post_name: disruptor-thrift-server-link-param-rps
status: publish
post_type: post

# disruptor thrift server连接参数与rps数值影响记录

![disruptor thrift server](http://chen54.b0.upaiyun.com/0122/267176d6-5ef4-3e0b-937e-c492f5ba7807.jpg) **基础环境** rmbp 8G MEM Intel(R) Core(TM) i7-3615QM CPU @ 2.30GHz 四核 八线程 oracle jdk 1.7.0_45 **原理** 如上图，是类似tomcat的nio实现过程，不过将queue换成了高性能的disruptor，可能会得到更好的性能。可调整参数为numAcceptors，numSelectors，numWorkersPerSelector三个值。 测试的代码位置在 https://github.com/54chen/disruptor_thrift_server 项目中的BenchmarkTest同时启动一个server，同时对其进行压测。 AbstractDisruptorTest.Service类中实现了server的逻辑，里面实际上是一个加减乘除的简单方法，故意加了300ms的sleep以尽可能模拟真实的消费时间。 BenchmarkTest类中实现了client的压力逻辑，固定了200个线程同时死循环地进行访问，同时进行计数统计rps。 **调整记录** 1.A(cceptors):4 S(electors):2 W(orkersPerSelector):2 处理接入的线程数:4 处理调用逻辑的线程数:2 推荐是cpu的核数以上 每个处理逻辑上的worker数量:2 结果： Rate: 16 req/s Rate: 12 req/s Rate: 12 req/s 无reset等异常。 2.A:40 S:2 W:2 结果： Rate: 16 req/s Rate: 12 req/s Rate: 12 req/s 4.A:40 S:8 W:2 Rate: 48 req/s cpu:10% 线程数:287 5.A:40 S:80 W:2 Rate: 385 req/s cpu:25% 线程数:503 6.A:40 S:100 W:2 Rate: 400-500 req/s cpu:30% 线程数:563 开始出现reset异常 java.net.SocketException: Connection reset 7.A:40 S:100 W:4 Rate: 600-700 req/s cpu:50% 线程数:763 开始出现java.net.NoRouteToHostException: Can't assign requested address 极其不稳定，还会出现too many open files的异常 这是因为客户端同服务器端在同一机器的原因，受到了ulimit之类的限制 还出现了java.net.ConnectException: Operation timed out **后记** 上面的尝试都是在我的笔记本上进行的，因为mac不像centos这么好调整ulimit maxsockets这些参数，所以点到即止。 简单说，A值是水龙头，S值是分水管道，W值是发电机，W的值与CPU的负载有直接关系，所以一般以CPU的核数来对这个值进行调优。