title: 利用h2database和easymock轻松不依赖环境单元测试（rose等spring环境万能）
link: http://www.54chen.com/java-ee/h2database-easymock-unit-test.html
author: 54chen
description: 
post_id: 2134
created: 2012/08/01 18:15:23
created_gmt: 2012/08/01 10:15:23
comment_status: open
post_name: h2database-easymock-unit-test
status: publish
post_type: post

# 利用h2database和easymock轻松不依赖环境单元测试（rose等spring环境万能）

![h2database,easymock](http://img04.taobaocdn.com/imgextra/i4/T1LbWjXndsXXccfjjX_085624.jpg) **前言** 写java时间久了，慢慢就变得自信了，也可能是变得懒了，或者是项目进度的原因，test case越写越少，越来越不写了。 还有一个很大的原因是，DB环境不好搞，老是有垃圾数据，依赖的东西太多，有memcache、有别人的服务，等等等等，都是没有test case良好的理由。 下面将以rose环境下，使用easy mock搞写第三方依赖和h2database搞写DB环境为例子，简单讲述如何轻松测试。 完整代码可以在 <https://github.com/XiaoMi/rose> 下载到。 **依赖的包介绍** spring-test：提供一些基础的spring环境的支持，如果你不是spring环境，可能不需要。 h2：一个内存数据库，使用它的原因是，我们的业务大多数时候使用的都是mysql，而h2有一个mysql模式，可以支持绝大多数的mysql的SQL语句。 easymock：以前在五四陈科学院提到过的利器，用来模拟各种调用的工具。 pom定义为： 

  1.       <dependency>  
  2.     <groupId>org.springframework</groupId>  
  3.     <artifactId>spring-test</artifactId>  
  4.     <version>2.5.6</version>  
  5.     <scope>test</scope>  
  6. </dependency>  
  7. <dependency>  
  8.     <groupId>com.h2database</groupId>  
  9.     <artifactId>h2</artifactId>  
  10.     <version>1.3.163</version>  
  11.     <scope>test</scope>  
  12. </dependency>    
  13. <dependency>  
  14.     <groupId>org.easymock</groupId>  
  15.     <artifactId>easymockclassextension</artifactId>  
  16.     <version>2.4</version>  
  17.     <scope>test</scope>  
  18. </dependency>  

**基础配置与schema.sql准备** 在test/resources下我们需要两个关键的文件：applicationContext.xml 与 schema.sql applicationContext.xml定义了test执行时扫描的情况，同正常运行时使用的配置大同小异。不同在于需要替换db定义中的mysql driver为h2的driver，关键部分： 

  1. <property name="driverClassName" value="org.h2.Driver"></property>  
  2. <property name="url" value="jdbc:h2:mem:testdb;MODE=MYSQL;DB_CLOSE_DELAY=-1"></property>    

而schema.sql文件定义了初始化的表结构，这个文件的出现是因为h2与mysql还是有一定的语法区别的，不过在我的使用过程中，基本上只有create table的那个引擎声明是不支持的，所以喽，干掉先。 **H2下的DAO的Test的代码示例** 在test case的初始化状态里需要导入schema.sql，代码如下：

## Comments

**[54chen](#15440 "2013-03-18 20:31:50"):** spring允许在他们的管理过程中介入mock对象的。ReflectionTestUtils.setField

**[zero](#15429 "2013-03-16 00:31:46"):** 请问使用maven跑test时，在测试DAO的时候，我看代码是直接注入了TestDAO，这个不是rose负责实例化的吗，但是我并没有看到rose初始化的地方

**[sai](#14994 "2012-08-04 22:32:46"):** 这个是直接本地测试吗？那jade的dao，rose的controller是怎么初始化的？

**[54chen](#14997 "2012-08-07 12:29:40"):** 还没有对controller层进行测试。

