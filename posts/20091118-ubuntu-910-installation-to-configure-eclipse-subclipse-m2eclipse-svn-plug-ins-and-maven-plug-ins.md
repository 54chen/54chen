title: ubuntu 9.10安装配置eclipse、subclipse、m2eclipse(svn插件和maven插件)
link: http://www.54chen.com/life/ubuntu-910-installation-to-configure-eclipse-subclipse-m2eclipse-svn-plug-ins-and-maven-plug-ins.html
author: cc0cc
description: 
post_id: 833
created: 2009/11/18 19:38:43
created_gmt: 2009/11/18 11:38:43
comment_status: open
post_name: ubuntu-910-installation-to-configure-eclipse-subclipse-m2eclipse-svn-plug-ins-and-maven-plug-ins
status: publish
post_type: post

# ubuntu 9.10安装配置eclipse、subclipse、m2eclipse(svn插件和maven插件)

ntfs硬盘安装ubuntu 9.10方案见： [x200 ntfs硬盘安装ubuntu9.10正式版手记](/830-x200-ntfs-hard-disk-to-install-the-official-version-of-notes-from-ubuntu910/) eclispe 3.51安装： 

> sudo apt-get install eclipse

整完会在应用程序中多出来一项“编程”。 此时的eclipse是以自带的jre来运行的，后面的maven插件会要求eclipse在jdk里运行，而不是jre里，所以再安装一下jdk: 

> sudo apt-get install sun-java6-jdk

这样，jdk会安装到/usr/lib/jvm/java-6-sun 然后修改eclipse.ini 

> sudo vim /usr/lib/eclipse/eclipse.ini

在-vmargs上一行加入： 

> -vm /usr/lib/jvm/java-6-sun-1.6.0.15/bin (有回车)

同时选择一下window->preferences->java->installed jres增加对应目录的jre 然后开始 Help->install new software subclipse http://subclipse.tigris.org/update_1.2.x 安装好subclipse。subclipse提供了svn的支持。 然后同样安装m2eclipse插件： m2eclipse比较麻烦，需要依次安装的有： 

> emf http://download.eclipse.org/modeling/emf/updates/releases/ cdt http://download.eclipse.org/tools/cdt/releases/new wtp http://download.eclipse.org/webtools/updates ajdt http://download.eclipse.org/tools/ajdt/34/update zest or GEF http://download.eclipse.org/tools/gef/updates/releases/ mylyn http://download.eclipse.org/tools/mylyn/update/e3.4

最后才是m2eclipse: 

> http://m2eclipse.sonatype.org/update/

注意，在安装过程中，有些update的url里有不止一个版本的选项，注意适当选取。