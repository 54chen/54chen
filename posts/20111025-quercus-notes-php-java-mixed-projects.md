title: quercus记录：php和java的混合型项目建立手记
link: http://www.54chen.com/php-tech/quercus-notes-php-java-mixed-projects.html
author: 54chen
description: 
post_id: 1889
created: 2011/10/25 21:30:55
created_gmt: 2011/10/25 13:30:55
comment_status: open
post_name: quercus-notes-php-java-mixed-projects
status: publish
post_type: post

# quercus记录：php和java的混合型项目建立手记

创业公司参与项目的人口众多、背景不一，目前市场上的主流方向为php与java，很多时候java工程师恨铁不成钢，php工程师也无可奈何。于是便有了此文，讲述如何使用quercus创建php java混合型项目。 **quercus是什么？** quercus是Caucho公司针对php语言的java实现，100%完成了php5的解析。是resin内建支持的功能。同时，因为使用了resin，使得php可以很容易得到连接池、分布式session、负载均衡等功能。使用resin的php项目可以更加安全，不存在很多fastcgi的问题。 **性能如何？** 官方：用mediawiki与drupal来做实验，要比mod_php快**4**倍。 有一个编译选项，在resin专业版里支持，可以把php转成java class，得到更高性能。 **新建一个java项目混合php项目** web.xml是关键，里面声明了*.php文件的访问都以com.caucho.quercus.servlet.QuercusServlet来执行。 

  1. <?xml version="1.0" encoding="UTF-8"?>  
  2. <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  3.     xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"  
  4.     xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"  
  5.     id="WebApp_ID" version="2.5">  
  6.     <display-name>testquercus</display-name>  
  7.     <welcome-file-list>  
  8.         <welcome-file>index.html</welcome-file>  
  9.         <welcome-file>index.php</welcome-file>  
  10.     </welcome-file-list>  
  11.     <servlet>  
  12.         <servlet-name>Quercus Servlet</servlet-name>  
  13.         <servlet-class>com.caucho.quercus.servlet.QuercusServlet</servlet-class>   
  14.     </servlet>  
  15.     <servlet-mapping>  
  16.         <servlet-name>Quercus Servlet</servlet-name>  
  17.         <url-pattern>*.php</url-pattern>  
  18.     </servlet-mapping>   
  19. </web-app>  

所有的php文件放在webapp下，所有的java文件还放到java目录里。 如何混合java到php中? 在java目录新建类Test54Chen，生成后位置： WEB-INF/classes/com/chen/FrameWork.class 

  1. /** 
  2.  * @author 54chen(陈臻) [chenzhen@xiaomi.com czhttp@gmail.com] 
  3.  * @since 2011-10-25 下午08:40:49 
  4.  */  
  5. package com.chen;  
  6.   
  7. public class FrameWork {  
  8.     public String go(String who) {  
  9.         return who + " gogogo!";  
  10.     }  
  11. }  

在webapp新建php文件： test.php

## Comments

**[54chen](#13948 "2011-11-01 19:38:13"):** 的确如此

**[is](#13938 "2011-10-29 01:41:47"):** 这个玩意如果不涉及字符编码问题还是个不错的东西。

**[dk01](#13971 "2011-11-08 19:27:24"):** 这个东西果然很有意思，融合java和php，基本上囊括了当前主流的web开发平台了。

**[chenqj](#13995 "2011-11-18 09:56:46"):** 很棒的东东，有没有考虑php作为前端展示，java作为后端服务，通过thrift结合的开发模式呢？

