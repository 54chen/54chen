title: [五四陈]java项目管理神兵利器maven和nexus使用手记
link: http://www.54chen.com/java-ee/54-chen-java-magic-weapon-maven-project-management-and-the-nexus.html
author: cc0cc
description: 
post_id: 772
created: 2009/09/15 12:39:42
created_gmt: 2009/09/15 04:39:42
comment_status: open
post_name: 54-chen-java-magic-weapon-maven-project-management-and-the-nexus
status: publish
post_type: post

# [五四陈]java项目管理神兵利器maven和nexus使用手记

"尽管 Ant 对于构建 Java 程序而言是事实上的标准工具，但这个工具在许多方面都不胜任项目管理任务。相反，Ant 提供的东西，Maven（出自 Apache Jakarta 项目的高级项目管理工具）都能提供，而且更多。" --转自IBM开发者网络 **适用场景** 如果你的项目互相有jar包的依赖，WEB-INF/lib下有大量杂乱的jar包，svn里lib目录下时常要更新东西，并且你为此伤透了脑筋，团队战斗力无法提升，那么，你就需要这两样利器了。 **主角出场 windows环境居多** <http://maven.apache.org/> 项目的主页，有洋文 <http://maven.apache.org/download.html> 下载的地方 安装的说明-好yy的居然有中文！ <http://www.sonatype.com/books/maven-book/reference_zh/installation.html> 大概意思就是下一个包然后设置环境变量。主要目的就是让你的本地环境能够执行mvn。 **跑龙套的出场 linux环境犹佳** <http://nexus.sonatype.org/> 项目的主页，有洋文 <http://nexus.sonatype.org/downloads/> 下载的地方 安装的说明 <http://www.sonatype.com/books/nexus-book/reference/install.html> 就是下一个包然后以webserver方式运行下。主要目录是让你的jar包能有个放处。 类似 ./bin/jsw/macosx-universal-32/nexus start 安装的视频  全屏才能看清楚. 跑龙套的近照 默认密码是 admin/admin123 ![](http://www.sonatype.com/books/nexus-book/reference/figs/web/repository-manager_nexus-login.png) **剧本** 主角其实是个客户端工具，所有的功能都集中于一个命令：mvn 例如：运行cmd 进入项目文件夹 mvn package 将项目打包  mvn install 将项目包安装到本地仓库 mvn deploy 向包布到跑龙套那个东东上面。 mvn命令还有很多强大的功能，具体见 <http://www.sonatype.com/books/maven-book/reference_zh/public-book.html> 跑龙套那个其实才算得上第二主角，它是一个强大的服务器端，它管理着你所有原来在svn里的lib下的所有jar包。并且很重要的一点，它能通过web界面搜索。 这样，只要大家都使用和发布snapshot版本的jar包，不需要再另行通知更新jar包，开发的人搞好直接mvn deploy，使用的人在mvn -U clean package 自然就拖下最新的包，默契不在话下。 这一切，都来源于pom组织的强大定义。【全剧终】 **鸣谢** 党和人民 老毛子精细的技术活

## Comments

**[cling](#11873 "2009-09-15 22:44:34"):** 好文

**[cc0cc](#11874 "2009-09-15 22:47:28"):** 好在哪里了 呵呵

