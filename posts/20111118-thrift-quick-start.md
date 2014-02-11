title: thrift快速入门实例
link: http://www.54chen.com/java-ee/thrift-quick-start.html
author: 54chen
description: 
post_id: 1901
created: 2011/11/18 13:30:22
created_gmt: 2011/11/18 05:30:22
comment_status: open
post_name: thrift-quick-start
status: publish
post_type: post

# thrift快速入门实例

![facebook thrift](http://img04.taobaocdn.com/imgextra/i4/T1gzmqXXdpXXcwYVo0_034121.jpg) Thrift是Facebook的核心框架之一，使不同的开发语言开发的系统可以通过该框架实现彼此的通信，类似于webservice，但是Thrift提供了近乎变态的效率和开发的方便性，是webservice所不能比拟的。给分布式开发带来了极大的方便。但是这柄利器也有一些不完美。 **安装thrift**

> 1、./configure --with-boost=/usr/include 2、make 3、make install

**定义接口文件**

  1. vim chen.thrift  
  2.   
  3. #!/usr/local/bin/thrift \--gen java  
  4. namespace java com.chen  
  5. service Hello{  
  6.         i32 hello()  
  7. }  

**生成代码**

  1. thrift \--gen java chen.thrift   

要使用1.6的jdk ** 实现server代码：**

  1. /** 
  2.  * @author 54chen(陈臻) [chenzhen@xiaomi.com czhttp@gmail.com] 
  3.  * @since 2011-6-24 下午08:11:15 
  4.  */  
  5. package com.chen;  
  6.   
  7. import org.apache.thrift.TException;  
  8.   
  9. public class HelloImpl implements Hello.Iface {  
  10.   
  11.     public void HelloImpl() {  
  12.     }  
  13.     public int hello() throws TException {  
  14.         System.out.println("hello 54chen");  
  15.         return 0;  
  16.     }  
  17.   
  18. }  

**通过下面的代码启动server**

  1. TServerSocket serverTransport = new TServerSocket(7911);  
  2.        Hello.Processor processor = new Hello.Processor(new HelloImpl());  
  3.        Factory protFactory = new TBinaryProtocol.Factory(true, true);  
  4.        TServer server = new TThreadPoolServer(processor, serverTransport, protFactory);  
  5.        System.out.println("Starting server on port 7911 ...");  
  6.   
  7.        server.serve();  

**通过下面代码启动client**

  1. TTransport transport = new TSocket("localhost", 7911);  
  2.       TProtocol protocol = new TBinaryProtocol(transport);  
  3.       Hello.Client client = new Hello.Client(protocol);  
  4.       transport.open();  
  5.       System.out.println("Client calls hello()");  
  6.       client.hello();  
  7.       transport.close();

## Comments

**[nodexy](#14041 "2011-11-30 16:27:39"):** 哇塞，这个快速入门可真够快的，哈哈，安装完了hello一下就没了。 thrift还是很不错的，很多B2C啊等系统后面的各种service也选择thrift以及ICE等，方便跨语言的共用service访问。 我也用过，java服务端，php和java客户端。 很不错！

