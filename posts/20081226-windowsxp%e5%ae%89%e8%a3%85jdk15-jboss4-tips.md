title: windowsXP安装jdk1.5 jboss4 antx tips
link: http://www.54chen.com/web-ral/windowsxp%e5%ae%89%e8%a3%85jdk15-jboss4-tips.html
author: cc0cc
description: 
post_id: 364
created: 2008/12/26 10:13:09
created_gmt: 2008/12/26 02:13:09
comment_status: open
post_name: windowsxp%e5%ae%89%e8%a3%85jdk15-jboss4-tips
status: publish
post_type: post

# windowsXP安装jdk1.5 jboss4 antx tips

1.jdk 1.5(也就是5) 文件名：java_ee_sdk-5_01-windows.exe 下载地址：http://cds-esd.sun.com/ESD28/JSCDL/java_ee_sdk/5.0_01-fcs/java_ee_sdk-5_01-windows.exe?AuthParam=1230186860_df7d73a40e743135e1323ad74ae1cd16&TicketId=B%2Fw5lx%2BGTl1OShVEMlBdkgPg&GroupName=CDS&FilePath=/ESD28/JSCDL/java_ee_sdk/5.0_01-fcs/java_ee_sdk-5_01-windows.exe&File=java_ee_sdk-5_01-windows.exe 双击安装至：C:\Sun\SDK 环境变量设置： JAVA_HOME：C:\Sun\SDK\jdk classpath：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar; PATH：%JAVA_HOME%\bin;   2.jboss 4.02 文件名：jboss-4.0.2.zip 下载地址：http://nchc.dl.sourceforge.net/sourceforge/jboss/jboss-4.0.2.zip 双击解压缩到：D:\jboss\ 环境变量： JBOSS_PATH：D:\jboss\jboss-4.0.2   3.antx svn co .... antx 目录：D:\antx 运行：build.bat 环境变量： ANTX_HOME：D:\antx\dist\antx PATH：D:\antx\dist\antx\bin   4.hesper 代码： svn co hesper 配置antx构建工程：C:\Documents and Settings\dongfangbai\antx.properties 

> antx.properties释义： antx.repository.project  = d:/antx/repository.project(二方库的位置，修改成你自己的) hesper.output  = D:/work/logs/hesper（hesper的日志指定目录，修改成你自己的） hesper.vmcommon.path  = D:/work/vmcommon（公共模板文件目录，修改成你自己的）

进入代码文件夹构建工程：antx reactor goals=default,eclipse 导入eclipse：导入现有的工程->指向hesper主目录   5.ear发布至jboss 修改jboss配置(修改默认的发布目录为自己的开发目录，这样不用来回复制了)： D:\jboss\jboss-4.0.2\server\default\conf\jboss-service.xml 

>  <attribute name="URLs">          deploy/,file:/D:\dev\hesper\deploy\target（你自己的目录）  </attribute>  

修改jboss配置（修改默认端口为80）： D:\jboss\jboss-4.0.2\server\default\deploy\jbossweb-tomcat55.sar\server.xml   

>  <Connector port="80" address="${jboss.bind.address}"          maxThreads="250" strategy="ms" maxHttpHeaderSize="8192"          emptySessionPath="true"          enableLookups="false" redirectPort="8443" acceptCount="100"          connectionTimeout="20000" disableUploadTimeout="true"/>

    修改jboss配置（加虚拟目录，这个是意思意思...说不好哪天会使用...）： D:\jboss\jboss-4.0.2\server\default\deploy\jbossweb-tomcat55.sar\server.xml 找到<Host name="localhost"             autoDeploy="false" deployOnStartup="false" deployXML="false"> 下一行添加： 

> <Context   path="/h"   docBase="D:\dev\"   debug="0"   reloadable="false"/>

## Comments

**[kiss](#101 "2008-12-26 10:21:18"):** 很好，很实用。

**[anne](#102 "2008-12-26 10:24:19"):** 都是啥意思啊，也太专业了

