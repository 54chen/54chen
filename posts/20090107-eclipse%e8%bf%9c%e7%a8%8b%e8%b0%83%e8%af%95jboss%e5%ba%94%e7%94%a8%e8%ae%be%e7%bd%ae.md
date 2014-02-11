title: Eclipse远程调试JBoss应用设置(linux&win版本)
link: http://www.54chen.com/java-ee/eclipse%e8%bf%9c%e7%a8%8b%e8%b0%83%e8%af%95jboss%e5%ba%94%e7%94%a8%e8%ae%be%e7%bd%ae.html
author: cc0cc
description: 
post_id: 381
created: 2009/01/07 11:03:13
created_gmt: 2009/01/07 03:03:13
comment_status: open
post_name: eclipse%e8%bf%9c%e7%a8%8b%e8%b0%83%e8%af%95jboss%e5%ba%94%e7%94%a8%e8%ae%be%e7%bd%ae
status: publish
post_type: post

# Eclipse远程调试JBoss应用设置(linux&win版本)

* 修改[JBoss](/c/364)启动配置
打开JBOSS_HOME/bin目录下的run.conf文件，找到：  #JAVA_OPTS="$JAVA_OPTS -Xdebug -Xrunjdwp:transport=dt_socket,address=8787,server=y,suspend=y" 将其修改为：  JAVA_OPTS="$JAVA_OPTS -Xdebug -Xrunjdwp:transport=dt_socket,address=8787,server=y,suspend=n" (最后的y改成n) 其中：8787为调试的端口号； **WIN:** 直接修改run.bat，找到这一行，删除前面的rem，修改suspend为n。 

  *  启动JBoss
 ./run.sh -b192.168.1.x 其中192.168.1.x是Jboss所在机器的ip； 
  * 新建调试配置
在Eclipse中，[打开](/c/47)菜单“run”-〉“debug configurations...”；在出现的对话框中，选择“Remote Java Application”，右键单击，在弹出的菜单中选择“New”，在出现的对话框的“host”中输入Jboss[服务器](http://www.54chen.com/c/379)的ip（192.168.1.x），在“port”中输入调试的端口号（8787），选择“debug”开始调试； 

  *   调试
在出现的“debug view”中，打开需要调试的[Java](/c/354)源文件，设置相应的断点就可以了