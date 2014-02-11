title: 持续构建工具hudson使用手记
link: http://www.54chen.com/java-ee/ci-tool-hudson-usage.html
author: 54chen
description: 
post_id: 1763
created: 2011/07/07 09:23:53
created_gmt: 2011/07/07 01:23:53
comment_status: open
post_name: ci-tool-hudson-usage
status: publish
post_type: post

# 持续构建工具hudson使用手记

![hudson](http://img04.taobaocdn.com/imgextra/i4/T1RgNyXjFsXXXG8Bva_092141.jpg) hudson是一个强大的持续性构建服务器。（现在开发者们分了一个分支，改叫Jenkins。）我（54chen）在使用过程中的手记如下： **下载** 地址：http://hudson-ci.org/ 

> wget http://java.net/projects/hudson/downloads/download/war/hudson-2.0.1.war

速度不咋的，60k/s. **先决条件** 系统：centos 5.5 

> resin4下不work，换成了tomcat。

> cp /opt/soft/hudson/hudson-2.0.1.war /opt/soft/apache-tomcat-7.0.16/webapps/

**遇到的问题** 1）tomcat使用utf8环境。 vim tomcat/conf/server.xml 找到8080，加入一行 URIEncoding="UTF-8"。 2）maven不工作 hudson启动后，系统管理里面，设置maven的地址：MAVEN_HOME指到maven的主目录，不是bin目录。 3）svn不工作 报re_version错误，在你的svn地址的最后面加上@HEAD 4）hudson一切正常后，有svn bug 

> FATAL: tried to access method hudson.scm.ChangeLogSet$Entry.setParent(Lhudson/scm/ChangeLogSet;)V from class hudson.scm.SubversionChangeLogSet 

报上面的错误，我（54chen）在http://issues.hudson-ci.org/browse/HUDSON-8885里找到更新新的svn插件即可。进入到系统管理的插件管理中，选中svn 插件，安装更新即可。 5）定时执行 建立job时，有一个选项是Build Triggers，勾选 Build periodically ，schedule填写：0 10,22 * * *表示每天10/22点执行。 6）重启后jobs丢失 jobs都是定义在$HUDSON_HOME里的，如果一开始的时候没有设置，jobs将放在~/.hudson下，当重启的时候，可能发生各种找不到原来的jobs的问题，此时需要确定原来的HUDSON_HOME是啥，现在的是啥，如果不一致就会出问题。