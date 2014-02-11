title: scala入门手记
link: http://www.54chen.com/_linux_/scala-notes.html
author: 54chen
description: 
post_id: 2281
created: 2013/01/14 18:55:19
created_gmt: 2013/01/14 10:55:19
comment_status: open
post_name: scala-notes
status: publish
post_type: post

# scala入门手记

![scala](http://img03.taobaocdn.com/imgextra/i3/T1.1RSXmRfXXXjG9sV_021627.jpg) **一、安装环境**

> localhost:~ chenzhen$ java -version java version "1.6.0_37" Java(TM) SE Runtime Environment (build 1.6.0_37-b06-434-11M3909) Java HotSpot(TM) 64-Bit Server VM (build 20.12-b01-434, mixed mode)

如果java没有安装，需要先安装。此处略去N字。 <http://www.scala-lang.org/downloads> 下载最新的版本。翻墙自理。 $tar zxvf scala-2.10.0.tgz 解压后，添加环境变量到配置文件中。 

> vim ~/.profile (or .bashrc) export PATH=/Users/chenzhen/scala-2.10.0/bin:$PATH

> localhost:~ chenzhen$ scala -version Scala code runner version 2.10.0 -- Copyright 2002-2012, LAMP/EPFL 

**二、安装eclipse（4.2）插件** site url: 

> http://download.scala-ide.org/sdk/e38/scala210/dev/site/

一路install。 **三、hello world** 跟java项目有点像。 

> package com.hello object Cz { def main(args: Array[String]) { println("hello world"); } }

**四、语法对比速记** 1.scala的数组与java相比是可变的数据结构。 2.for(i <\- 0 to 2) 这里的向左箭头是指i的范围，两头是闭区间。 3.list是不可变的，不需要new，list与list之间用:::（三个冒号）可以做连接,::(两个冒号)会产生新的维度的list。 4.var可变，val不可变。崇尚val，不可变对象和没有副作用的方法。 5.Unit类似java的void。 6.object是特殊的class，是单例。 7.maven也可以搞定scala，但是更好用的是sbt。