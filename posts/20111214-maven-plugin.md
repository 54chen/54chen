title: maven插件编写实例
link: http://www.54chen.com/java-ee/maven-plugin.html
author: 54chen
description: 
post_id: 1927
created: 2011/12/14 20:33:39
created_gmt: 2011/12/14 12:33:39
comment_status: open
post_name: maven-plugin
status: publish
post_type: post

# maven插件编写实例

![apache maven](http://maven.apache.org/images/maventxt_logo_200.gif) **概况** 实现一个自定义的类，必须要继承自 org.apache.maven.plugin.AbstractMojo，同时实现execute方法。 maven使用了在注释中写代码，BT的写法，所以注释里的@goal @parameter都是有含义的。 **实例**

  1. mvn hello:gogo  

这样的一个命令，到代码里，应该是如下过程 **进入** hello被叫做plugin的prefix，maven会去寻找以hello开头的插件。 在插件的pom里必须定义其名字为hello开头的，例如： 

  1. <artifactId>hello-maven-plugin</artifactId>  

**goal目标** hello后面的gogo叫做goal，目标，则以注释中写代码的方式来完成： 

  1. /** 
  2. *@goal gogo 
  3. **/  
  4. HelloMojo extends AbstractMojo  

**需要的依赖**

  1. <dependency>  
  2.  <groupId>org.apache.maven</groupId>  
  3.  <artifactId>maven-plugin-api</artifactId>  
  4.  <version>3.0.3</version>  
  5. </dependency>  

**必需的选项**

  1. <packaging>maven-plugin</packaging>  

必需要是maven-plugin才能成功。 **参数** 如果要传什么参数给插件，应该是 

  1. /** 
  2. * if false all compression is off (default is true) 
  3. * 
  4. * @parameter expression="${hello.enabled}" default-value="true" 
  5. */  
  6. private Boolean enabled=true;  

**使用时项目定义** 然后在使用的项目里，需要定义的有 

  1. <plugin>  
  2.     <groupId>com.chen</groupId>  
  3.     <artifactId>demo-maven-plugin</artifactId>  
  4.     <version>1.0</version>  
  5.     <configuration>  
  6.         <goalPrefix>hello</goalPrefix>  
  7.         <enabled>false</enabled>  
  8.     </configuration>  
  9. </plugin>  

然后执行上述命令时自然可以执行HelloMojo的execute方法。