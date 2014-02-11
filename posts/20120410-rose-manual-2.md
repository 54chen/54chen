title: rose手册第二章：配置与使用
link: http://www.54chen.com/java-ee/rose-manual-2.html
author: 54chen
description: 
post_id: 2046
created: 2012/04/10 12:05:09
created_gmt: 2012/04/10 04:05:09
comment_status: open
post_name: rose-manual-2
status: publish
post_type: post

# rose手册第二章：配置与使用

ROSE

**2.1 基础环境** * 普通的pc机，del 380 * ubuntu 10.04基本不升级 * java version "1.6.0_29" * eclipse * m2clipse * 茶一杯 **2.2 maven简介** * maven是基于项目对象模型(POM)，可以通过一小段描述信息来管理项目的构建，报告和文档的软件项目管理工具。如果你已经有十次输入同样的Ant targets来编译你的代码、jar或者war、生成javadocs，你一定会自问，是否有一个重复性更少却能同样完成该工作的方法。Maven便提供了这样一种选择，将你的注意力从作业层转移到项目管理层。Maven项目已经能够知道如何构建和捆绑代码，运行测试，生成文档并宿主项目网页。 * maven对一个项目进入了固定的默认目录定义： * src/main/java 写主要的java实现 * src/main/resources 写主要的配置文件 * src/test/java 写test case * src/test/resources 写test case所需要的配置文件 * src/main/webapp [war项目特有]web项目的对外目录 * src/main/webapp/WEB-INF [war项目特有]web项目配置web.xml目录 **2.3 项目建立** * 打开eclipse(需要提前安装好m2clipse插件) * new -> other -> maven -> maven project * create a simple project * next * group id:com.54chen * artifact id:rose-example * packaging: war * finished **2.4 基础配置三步走** ** 1）点火：基础的pom文件 ** 打开2.3建立好的项目，打开pom.xml，添加下面的段落到project中： 

  1. <dependencies>  
  2.         <dependency>  
  3.             <groupId>com.54chen</groupId>  
  4.             <artifactId>paoding-rose-scanning</artifactId>  
  5.             <version>1.0-SNAPSHOT</version>  
  6.         </dependency>  
  7.   
  8.         <dependency>  
  9.             <groupId>com.54chen</groupId>  
  10.             <artifactId>paoding-rose</artifactId>  
  11.             <version>1.0-SNAPSHOT</version>  
  12.         </dependency>  
  13.   
  14.         <dependency>  
  15.             <groupId>com.54chen</groupId>  
  16.             <artifactId>paoding-rose-portal</artifactId>  
  17.             <version>1.0-SNAPSHOT</version>  
  18.         </dependency>  
  19.   
  20.         <dependency>  
  21.             <groupId>com.54chen</groupId>  
  22.             <artifactId>paoding-rose-jade</artifactId>  
  23.             <version>1.1-SNAPSHOT</version>  
  24.         </dependency>  
  25.     </dependencies>  

上述是rose环境最基础的依赖包。再添加一点常见的编译设置：

## Comments

**[tank](#14899 "2012-05-18 17:35:47"):** 请教哈表单验证是如何做的？不采用ajax的情况下！

**[mu0641](#15325 "2013-01-28 20:50:27"):** 我运行为debug as jetty run 修改程序可以自动加载立即在浏览器看到效果，但是修改注解@Path或者@Get要重新运行jetty才有效果，请问有什么好的解决办法吗

**[54chen](#15326 "2013-01-28 21:39:59"):** 没有太好的办法，可以考虑使用maven -jetty -plugin，每次mvn跑一下。

**[bigbin](#14920 "2012-06-16 13:27:05"):** 好像目前只有paoding-rose-scanning入maven中央库，不过还好在Google code都可以找到。

**[54chen](#14921 "2012-06-16 14:54:39"):** 嗯，你很细心，这两天会全部同步到中央库中。

