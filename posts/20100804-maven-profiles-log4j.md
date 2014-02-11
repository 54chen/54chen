title: 使用maven的profiles自动设置log4j线上环境和测试环境区别
link: http://www.54chen.com/life/maven-profiles-log4j.html
author: 54chen
description: 
post_id: 1253
created: 2010/08/04 18:45:52
created_gmt: 2010/08/04 10:45:52
comment_status: open
post_name: maven-profiles-log4j
status: publish
post_type: post

# 使用maven的profiles自动设置log4j线上环境和测试环境区别

![程序猿](http://img06.taobaocdn.com/imgextra/i6/T12CBHXXpLXXcRqLZ7_064514.jpg) **前言** Maven是基于项目对象模型(POM)，可以通过一小段描述信息来管理项目的构建，报告和文档的软件项目管理工具. 　　如果你已经有十次输入同样的Ant targets来编译你的代码、jar或者war、生成javadocs，你一定会自问，是否有一个重复性更少却能同样完成该工作的方法。Maven便提 供了这样一种选择，将你的注意力从作业层转移到项目管理层。Maven项目已经能够知道如何构建和捆绑代码，运行测试，生成文档并宿主项目网页. 项目的主页地址为：http://maven.apache.org/ **深入** Profiles是maven的一个很关键的术语：profile是用来定义一些在build lifecycle中使用的environmental variations，profile可以设置成在不同的环境下激活不同的profile（例如：不同的OS激活不同的profile，不同的JVM激活不同的profile，不同的dabase激活不同的profile等等）。 **实例** 测试机环境，搞一个文件，比如说/data/test。 线上环境，不搞这个文件。 然后pom在project下如下写法： 

> <profiles> <profile> <id>produce</id> <activation> <activeByDefault>false</activeByDefault> <file> <missing>/data/test</missing> </file> </activation> <properties> <log4j.log.path>/opt/logs/xxx.log</log4j.log.path> <log4j.debug.level>error</log4j.debug.level> </properties> </profile> <profile> <id>dev</id> <activation> <activeByDefault>true</activeByDefault> </activation> <properties> <log4j.log.path>/opt/logs/xxx.log</log4j.log.path> <log4j.debug.level>debug</log4j.debug.level> </properties> </profile> </profiles>

**log4j.xml文件设置** 为了配合maven的设置属性，需要将log4j中使用的声明用成变量，片段举例： 

> ﻿<logger name="com.xx.xx.xx.xx" additivity="false"> <level value="${log4j.debug.level}"/> <appender-ref ref="dao"/> </logger> <root> <level value="${log4j.debug.level}"/> <appender-ref ref="service"/> </root>

**解释** 这样，在运行mvn进行build项目的时候，如果线上环境，测试到/data/test文件是missing的，则会激活对应的设置，以此来达到线上环境改成error级别，而测试环境为debug级别。

## Comments

**[Fung](#12552 "2010-08-04 19:46:37"):** 哥，你能把这句话给改了不？人人网geek产品明星的博客-改成 “ Red.White.Black"或者改成别的，反正别说我是人人网的。。

**[wwek](#12600 "2010-08-07 22:13:00"):** 找不到留言的地方呢~~~~~ 右上角那个亮瞎双眼的 rss订阅量啊！！ 哈哈囧·· 交换个友情链接？你的很早就做好了， 关注你的web相关。 流水理鱼 http://www.iamle.com

**[54chen](#12627 "2010-08-09 11:03:12"):** 友情链接，我都以友情为基础，要不然先建立下友情吧

**[wwek](#12641 "2010-08-10 21:26:33"):** 呵呵。那是~~ 关注你博客中·

