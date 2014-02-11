title: senseidb使用手记
link: http://www.54chen.com/architecture/senseidb-notes.html
author: 54chen
description: 
post_id: 2023
created: 2012/03/13 18:55:05
created_gmt: 2012/03/13 10:55:05
comment_status: open
post_name: senseidb-notes
status: publish
post_type: post

# senseidb使用手记

![linkedin, senseidb](http://www.cgichina.com/uploads/allimg/c100706/12N3911E10-233O9.jpg) **下载** https://github.com/downloads/linkedin/sensei/sensei-1.0.0-release.tar.gz 这个版本是2012年1月份发布的，如果您看到此文时时间太久，请到他们的官方网站去寻找：http://senseidb.com **下载kafka** kafka：由linkedin开源的高吞吐量的消息系统。 http://people.apache.org/~nehanarkhede/kafka-0.7.0-incubating/kafka-0.7.0-incubating-src.tar.gz **下载中文分词包IKAnalyzer** http://code.google.com/p/ik-analyzer/downloads/list **编译sensei** tar zxvf sensei-1.0.0-release.tar.gz cd sensei-trunk ./bin/build.sh 或者 mvn package 要是没有maven客户端，需要自行安装（ubuntu下直接apt-get install maven） **编译kafka** tar zxvf kafka-0.7.0-incubating-src.tar.gz cd kafka-0.7.0-incubating-src/ ./sbt update pacakge **编译IKA** 新建一个jar包，里面只有一个类，内容如下： 

  1. public class IKAnalyzerFactory implements SenseiPluginFactory<IKAnalyzer> {    
  2.     @Override    
  3.     public IKAnalyzer getBean(Map<String, String> initProperties, String fullPrefix, SenseiPluginRegistry pluginRegistry) {    
  4.         return new IKAnalyzer();    
  5.     }    
  6. }    

** 配置sensei的sensei.properties** 重要的几点： 1）zookeeper的url: _sensei.cluster.url=zookeeper.n.miliao.com:2181_ 2）IKA的class设置: _sensei.index.analyzer.class = com.chen.IKA.IKAnalyzerFactory_ 3）kafka所使用的声明: _sensei.gateway.class=com.senseidb.gateway.kafka.KafkaDataProviderBuilder sensei.gateway.kafka.zookeeperUrl=zookeeper.n.miliao.com_ #下面的topic很关键，消息系统中用topic区别不同的消息 _sensei.gateway.kafka.topic=hotTopic_ #ProducerData生成时，也应该叫hotTopic **启动sensei** 略去了设置schema，可参考<http://www.54chen.com/java-ee/scalable-reltime-search-senseidb.html>. 

> cd sensei-trunk bin/start-sensei-node.sh example/xxx/conf

> (所有的使用的jar，比如说kafka\IKA及其依赖的包，都扔到conf/ext下)

**配置kafka的server.properties** 关键的一点： 1）设置kafka使用的zookeeper地址： 

> zk.connect=zookeeper.n.miliao.com:2181

**启动kafka**

> cd kafka-0.7.0-incubating-src nohup bin/kafka-server-start.sh config/server.properties &

** [如果你的schema定义无误，那么到这里你就可以通知kafka有消息进入，sensei就会自动开始消费这些消息进行索引等过程]** 在具体业务中传入数据参考前文：<http://www.54chen.com/java-ee/scalable-reltime-search-senseidb.html> ** 发起搜索**

  1. public static JSONArray doSearch(String key, int limit, int offset, String senseiServer) throws JSONException {  
  2.         SenseiClientRequest senseiRequest = SenseiClientRequest.builder().query(Queries.stringQuery(key)).paging(limit, offset)  
  3.                 .fetchStored(true).build();   
  4.         SenseiResult senseiResult = new SenseiServiceProxy(senseiServer, 8080).sendSearchRequest(senseiRequest);  
  5.         List<SenseiHit> list = senseiResult.getHits();  
  6.         JSONArray l = new JSONArray();  
  7.         for (int i = 0; i < list.size(); i++) {  
  8.             SenseiHit hit = list.get(i);  
  9.             l.put(new JSONObject(hit.getSrcdata()));  
  10.         }  
  11.         return l;  
  12.     }  

____EOF____ 2012.3.13 version 1