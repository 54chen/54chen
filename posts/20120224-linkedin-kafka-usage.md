title: linkedin高吞吐量分布式消息系统kafka使用手记
link: http://www.54chen.com/architecture/linkedin-kafka-usage.html
author: 54chen
description: 
post_id: 2010
created: 2012/02/24 18:50:38
created_gmt: 2012/02/24 10:50:38
comment_status: open
post_name: linkedin-kafka-usage
status: publish
post_type: post

# linkedin高吞吐量分布式消息系统kafka使用手记

![linkedin kafka](http://www.cgichina.com/uploads/allimg/c100706/12N3911E10-233O9.jpg) kafka是一种高吞吐量的分布式发布订阅消息系统，她有如下特性： 

> 通过O(1)的磁盘数据结构提供消息的持久化，这种结构对于即使数以TB的消息存储也能够保持长时间的稳定性能。 高吞吐量：即使是非常普通的硬件kafka也可以支持每秒数十万的消息。 支持通过kafka服务器和消费机集群来分区消息。 支持Hadoop并行数据加载。 

设计侧重高吞吐量，用于好友动态，相关性统计，排行统计，访问频率控制，批处理等系统。大部分的消息中间件能够处理实时性要求高的消息/数据，但是对于队列中大量未处理的消息/数据在持久性方面比较弱。 kakfa的consumer使用拉的方式工作。 **安装kafka** 下载：http://people.apache.org/~nehanarkhede/kafka-0.7.0-incubating/kafka-0.7.0-incubating-src.tar.gz > tar xzf kafka-.tgz > cd kafka- > ./sbt update > ./sbt package 启动zkserver: bin/zookeeper-server-start.sh config/zookeeper.properties 启动server: bin/kafka-server-start.sh config/server.properties 就是这么简单。 **使用kafka**

  1. import java.util.Arrays;  
  2. import java.util.List;  
  3. import java.util.Properties;  
  4. import kafka.javaapi.producer.SyncProducer;  
  5. import kafka.javaapi.message.ByteBufferMessageSet;  
  6. import kafka.message.Message;  
  7. import kafka.producer.SyncProducerConfig;  
  8.   
  9. ...  
  10.   
  11. Properties props = new Properties();  
  12. props.put(“zk.connect”, “127.0.0.1:2181”);  
  13. props.put("serializer.class", "kafka.serializer.StringEncoder");  
  14. ProducerConfig config = new ProducerConfig(props);  
  15. Producer<String, String> producer = new Producer<String, String>(config);  
  16.   
  17. Send a single message  
  18.   
  19. // The message is sent to a randomly selected partition registered in ZK  
  20. ProducerData<String, String> data = new ProducerData<String, String>("test-topic", "test-message");  
  21. producer.send(data);      
  22.   
  23. producer.close();  

这样就是一个标准的producer。 **consumer的代码**

## Comments

**[54chen](#15681 "2013-11-26 10:55:26"):** kafka\rabbitmq都有在使用。看不同的团队习惯。

**[saber](#15680 "2013-11-25 14:40:06"):** 小米内部的消息队列用的是什么？

**[saber](#15682 "2013-11-26 14:02:40"):** 那rabbitmq你们是怎么做高可用以及负载均衡的？

**[54chen](#15057 "2012-10-16 15:10:52"):** 有这种可能。

**[fuqingwu](#15056 "2012-10-16 14:34:37"):** 我是在虚拟机的linux中装的，首先会报： Getting org.scala-tools.sbt sbt_2.7.7 0.7.5 ... You probably access the destination server through a proxy server that is not well configured. 是不是跟虚拟机的上网有关系？

**[54chen](#15055 "2012-10-16 12:16:30"):** 可能是环境因素影响的，试试看这个说的：http://stackoverflow.com/questions/6778316/problem-install-lift-framework-on-fedora

**[fuqingwu](#15054 "2012-10-16 11:51:42"):** :::::::::::::::::::::::::::::::::::::::::::::: :: UNRESOLVED DEPENDENCIES :: :::::::::::::::::::::::::::::::::::::::::::::: :: org.scala-tools.sbt#sbt_2.7.7;0.7.5: not found :::::::::::::::::::::::::::::::::::::::::::::: 安装kafka遇到这个问题，请问怎么解决，网上查了很多资料

**[李冰](#15064 "2012-10-24 18:34:42"):** 你运行sbt 是应该运行的是./sbt 不是sbt

**[54chen](#15069 "2012-10-25 19:11:16"):** 被编辑器吞了，抱歉给你带来的影响。

