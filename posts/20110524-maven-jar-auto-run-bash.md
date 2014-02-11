title: maven打jar包自动生成run脚本-appassembler插件的使用
link: http://www.54chen.com/java-ee/maven-jar-auto-run-bash.html
author: 54chen
description: 
post_id: 1711
created: 2011/05/24 15:36:38
created_gmt: 2011/05/24 07:36:38
comment_status: open
post_name: maven-jar-auto-run-bash
status: publish
post_type: post

# maven打jar包自动生成run脚本-appassembler插件的使用

![](http://ww1.sinaimg.cn/bmiddle/569f6ce8jw1dhi9e5bj6wj.jpg) 关键配置： 

> <plugin> <groupId>org.codehaus.mojo</groupId> <artifactId>appassembler-maven-plugin</artifactId> <version>1.1.1</version> <configuration> <programs> <program> <mainClass>com.chen.logtailer.Tail</mainClass> <name>app</name> </program> </programs> </configuration> </plugin>

#mvn package appassembler:assemble #sh target/appassembler/bin/app **优点：** 不需要管理各种run的脚本，自动生成。

## Comments

**[张扬扬](#13571 "2011-05-24 17:37:41"):** 占个位置 支持下

**[hewenxiang](#13588 "2011-06-08 15:37:45"):** 为什么是雪碧！！！！！！！！！！！！

