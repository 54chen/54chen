title: 分布式实时搜索方案介绍-senseidb
link: http://www.54chen.com/architecture/scalable-reltime-search-senseidb.html
author: 54chen
description: 
post_id: 2015
created: 2012/02/27 21:07:38
created_gmt: 2012/02/27 13:07:38
comment_status: open
post_name: scalable-reltime-search-senseidb
status: publish
post_type: post

# 分布式实时搜索方案介绍-senseidb

![linkedin senseidb](http://www.cgichina.com/uploads/allimg/c100706/12N3911E10-233O9.jpg) **名词解释** zoie：由linkedin开源的建立在lucene之上提供实时索引的系统。它利用两 个内存索引一个硬盘索引来实现实时搜索。 bobo-browse：由linkedin开源的基于lucene的分类浏览搜索系统。 zookeeper：一个分布式的，开放源码的分布式应用程序协调服务，常用来做配置服务。 senseidb：开源，分布式，实时，半结构化的数据库（官方网站上如是说）。实际上是一个将zoie、bobo-browse、zookeeper整合起来，提供各种方便的使用办法的一个项目。项目目标是达到简单易用的分布式实时搜索系统。 kafka：由linkedin开源的高吞吐量的消息系统。 norbert：norbert是一个提供分布式集群服务的开发框架，具备集群管理功能，对开发简单的通信架构，易扩展能承受高吞吐量的框架。scala实现，java无缝使用。其原理是：netty+zookeeper+pb。 IKAnalyzer：中文分词较好用的一个。 lucene：这个不用说了。 **使用senseidb** 1.解决中文分词问题 senseidb支持在配置上进行自定义analyzer。 要做的事情就是，建立一个jar包，里面只要一个类即可，依赖IKA的包。代码如下： 

  1. public class IKAnalyzerFactory implements SenseiPluginFactory<IKAnalyzer> {  
  2.     @Override  
  3.     public IKAnalyzer getBean(Map<String, String> initProperties, String fullPrefix, SenseiPluginRegistry pluginRegistry) {  
  4.         return new IKAnalyzer();  
  5.     }  
  6. }  

将此jar包放于sensei/conf/ext目录下，修改定义文件sensei.properties: sensei.index.analyzer.class = 上述jar包的全packagename classname 2.使用kafka数据源 在senseidb中有个叫gateway的概念，定义了数据源（实时的写入删除等）。 修改定义文件sensei.properties： sensei.gateway.class=com.senseidb.gateway.kafka.KafkaDataProviderBuilder 这个class存在于sensei-trunk/sensei-gateway，打包扔进conf/ext即可。 在具体业务中传入数据： 

  1. Properties props = new Properties();  
  2.         props.put("zk.connect", "your zk server:2181");  
  3.         props.put("serializer.class", "kafka.serializer.StringEncoder");  
  4.         ProducerConfig config = new ProducerConfig(props);  
  5.         Producer<String, String> producer = new Producer<String, String>(config);  
  6.   
  7.         int i = (int) (Math.random() * 10000);  
  8.   
  9.         JSONObject jo = new JSONObject();  
  10.         jo.put("thread_id", i);  
  11.         jo.put("hot_id", i + 300);  
  12.         jo.put("user_id", i + 1000);  
  13.         jo.put("type", i);  
  14.         jo.put("subject", "这是一个标题" + index);  
  15.         jo.put("contents", index);  
  16.   
  17.         System.out.println(i);  
  18.   
  19.         String msg = jo.toString();  
  20.         // The message is sent to a randomly selected partition registered in ZK  
  21.         ProducerData<String, String> data = new ProducerData<String, String>("hotTopic", msg);  
  22.         producer.send(data);  
  23.   
  24.         producer.close();  

3.索引配置 conf/schema.xml文件中定义了两种结构，一个是table一个是facets。 table的column定义了每个字段。 如下的一个定义，配合了2中的写入：

## Comments

**[zero](#14959 "2012-07-05 21:52:40"):** 现在senseidb具备投入到生产环境中吗

**[54chen](#14961 "2012-07-06 11:14:47"):** 为什么不呢？从linkedin回来的senseidb的作者之一baoqiu已经加入了我们的team。

