title: zookeeper客户端curator使用手记
link: http://www.54chen.com/java-ee/zookeeper-curator.html
author: 54chen
description: 
post_id: 2128
created: 2012/07/25 10:51:58
created_gmt: 2012/07/25 02:51:58
comment_status: open
post_name: zookeeper-curator
status: publish
post_type: post

# zookeeper客户端curator使用手记

![zookeeper](http://img03.taobaocdn.com/imgextra/i3/T13M02XbRXXXX4FVU4_051626.jpg) **一、简介** curator是Netflix公司开源的一个Zookeeper client library，用于简化zookeeper客户端编程，包含一下几个模块： curator-client - zookeeper client封装，用于取代原生的zookeeper客户端，提供一些非常有用的客户端特性 curator-framework - zookeeper api的高层封装，大大简化zookeeper客户端编程，添加了例如zookeeper连接管理、重试机制等 curator-recipes - zookeeper recipes 基于curator-framework的实现（除2PC以外） 从github和maven上的消息来看，1.0.1的版本已经十分稳定，相对应的zk版本是3.3.x，还在开发中的版本是1.1.x，对应的版本是zk3.4.x。 **二、依赖**

  1. <dependency>  
  2.             <groupId>com.netflix.curator</groupId>  
  3.             <artifactId>curator-framework</artifactId>  
  4.             <version>1.0.1</version>  
  5.         </dependency>  

** 三、代码讲解** 以下代码以CuratorFramework为例： 

  1. public static void main(String[] args) throws Exception {  
  2.      String path = "/test_path";  
  3.      CuratorFramework client = CuratorFrameworkFactory.builder().connectString("zookeeper.n.miliao.com:2181").namespace("/brokers").retryPolicy(new RetryNTimes(Integer.MAX_VALUE, 1000)).connectionTimeoutMs(5000).build();  
  4.      // 启动 上面的namespace会作为一个最根的节点在使用时自动创建  
  5.      client.start();  
  6.   
  7.      // 创建一个节点  
  8.      client.create().forPath("/head", new byte[0]);  
  9.   
  10.      // 异步地删除一个节点  
  11.      client.delete().inBackground().forPath("/head");  
  12.   
  13.      // 创建一个临时节点  
  14.      client.create().withMode(CreateMode.EPHEMERAL_SEQUENTIAL).forPath("/head/child", new byte[0]);  
  15.   
  16.      // 取数据  
  17.      client.getData().watched().inBackground().forPath("/test");  
  18.   
  19.      // 检查路径是否存在  
  20.      client.checkExists().forPath(path);  
  21.   
  22.      // 异步删除  
  23.      client.delete().inBackground().forPath("/head");  
  24.   
  25.      // 注册观察者，当节点变动时触发  
  26.      client.getData().usingWatcher(new Watcher() {  
  27.          @Override  
  28.          public void process(WatchedEvent event) {  
  29.              System.out.println("node is changed");  
  30.          }  
  31.      }).inBackground().forPath("/test");  
  32.   
  33.      // 结束使用  
  34.      client.close();  
  35.  }  

**四、方法说明 ** create(): 发起一个create操作. 可以组合其他方法 (比如mode 或background) 最后以forPath()方法结尾 delete(): 发起一个删除操作. 可以组合其他方法(version 或background) 最后以forPath()方法结尾 checkExists(): 发起一个检查ZNode 是否存在的操作. 可以组合其他方法(watch 或background) 最后以forPath()方法结尾

## Comments

**[zero](#14991 "2012-07-25 19:19:50"):** 赞，试试看

**[54chen](#15369 "2013-03-06 20:26:31"):** 没有遇到过

**[BabyDuncan](#15368 "2013-03-06 19:47:40"):** hi，五四陈，我最近在研究这个，发现在使用PathChildrenCache的时候，可以监听第一层ChildData的变化，我想寻求一个递归监听的办法，或者至少2层。 还有一个问题，就是PathChildrenCache.start()之后，第一次取得数据为空，等watcher都执行完之后，数据才准确，要是watcher没有执行完的话，数据就为空或不全，不知道你是否也遇到过这个问题 ，有没有解决办法。

