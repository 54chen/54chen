title: maven项目代码质量检测工具sonar使用手记
link: http://www.54chen.com/java-ee/open-source-quality-management-platform-sonar-notes.html
author: 54chen
description: 
post_id: 1648
created: 2011/03/23 13:40:14
created_gmt: 2011/03/23 05:40:14
comment_status: open
post_name: open-source-quality-management-platform-sonar-notes
status: publish
post_type: post

# maven项目代码质量检测工具sonar使用手记

![sonar](http://img02.taobaocdn.com/imgextra/i2/T1o7d4XgVmXXa0tYUU_013741.jpg) Sonar是一个开源代码质量控制工具。把它加到现在的持续集成工具中就能够让你的java开发项目集中处于质量控制之下。下面是安装和使用记录 ： **修改.m2/settings.xml** 增加下边的内容到profiles段落中： 

> <profile> <id>sonar</id> <activation> <activeByDefault>true</activeByDefault> </activation> <properties> <!-- EXAMPLE FOR MYSQL --> <sonar.jdbc.url> jdbc:mysql://localhost:3306/sonar?useUnicode=true&amp;characterEncoding=utf8 </sonar.jdbc.url> <sonar.jdbc.driverClassName>com.mysql.jdbc.Driver</sonar.jdbc.driverClassName> <sonar.jdbc.username>sonar</sonar.jdbc.username> <sonar.jdbc.password>sonar</sonar.jdbc.password> <!-- SERVER ON A REMOTE HOST --> <sonar.host.url>http://localhost:8888</sonar.host.url> </properties> </profile>

**增加mysql权限 ** GRANT all ON sonar.* TO sonar@localhost IDENTIFIED BY 'sonar'; FLUSH PRIVILEGES ; **vim .bashrc** export MAVEN_OPTS="-Xmx512m -XX:MaxPermSize=128m -Duser.timezone=Asia/Shanghai" source .bashrc **下载sonar，配置mysql:**

> conf/sonar.properties sonar.jdbc.url: jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8 sonar.jdbc.driverClassName: com.mysql.jdbc.Driver sonar.jdbc.validationQuery: select 1

用户密码不用，默认都是sonar **配置启动的http端口**

> sonar.web.host : localhost sonar.web.port: 8888 sonar.web.context: /

**启动服务：**

> bin/[YOUR PLATEFORM]/sonar.sh start

**开始使用：**

> 进到项目里运行 mvn sonar:sonar 打开http://localhost:8888 即可看到对应项目里的代码质量和修改建议。

**解决系统时区的问题：** vim sonar-xx/conf/wrapper.conf wrapper.java.additional.3=-Duser.timezone=Asia/Shanghai

## Comments

**[dan.liu](#13493 "2011-04-12 10:06:22"):** 哈哈我从国外的网站也看到了，最近也在关注，但是没有实用，。。。。

**[linda](#13511 "2011-04-18 11:01:28"):** 博主：你好！这个工具我安装了，可是不太会用，有时间可以交流一下吗？QQ：390273909

