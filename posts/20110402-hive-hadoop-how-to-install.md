title: hadoop hive安装手记
link: http://www.54chen.com/architecture/hive-hadoop-how-to-install.html
author: 54chen
description: 
post_id: 1658
created: 2011/04/02 13:50:04
created_gmt: 2011/04/02 05:50:04
comment_status: open
post_name: hive-hadoop-how-to-install
status: publish
post_type: post

# hadoop hive安装手记

![hadoop hive](http://img02.taobaocdn.com/imgextra/i2/T116J6XhxoXXcdrwMU_014654.jpg) hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供完整的sql查询功能，可以将sql语句转换为 MapReduce任务进行运行。 其优点是学习成本低，可以通过类SQL语句快速实现简单的MapReduce统计，不必开发专门的MapReduce应用，十分适合数据仓库的统计分析。 **[网络环境设置]** vim /etc/hosts 

> 192.168.100.52 hadoop1 192.168.99.34 hadoop2 192.168.103.135 hadoop3

分别到对应机器执行： 

> hostname hadoop1 hostname hadoop2 hostname hadoop3

**[打通机器]**

> hadoop1# ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa hadoop1# scp ~/.ssh/id_dsa.pub hadoop2:/root/ hadoop1# scp ~/.ssh/id_dsa.pub hadoop3:/root/ hadoop2# cat id_dsa.pub >> ~/.ssh/authorized_keys hadoop3# cat id_dsa.pub >> ~/.ssh/authorized_keys

验证：从hadoop1登录到hadoop2和hadoop3，不再需要密码。 **[安装hadoop]** 确保所有机器有 ssh rsync jdk 确保设置了： export JAVA_HOME=/opt/soft/jdk hive在0.20.x的hadoop做了大量的测试，因此选择0.20 

> cd /opt/soft/ wget http://mirror.bjtu.edu.cn/apache/hadoop/core/hadoop-0.20.2/hadoop-0.20.2.tar.gz tar -zxvf hadoop-0.20.2.tar.gz cd hadoop-0.20.2/ vim .bashrc export HADOOP_HOME=/opt/soft/hadoop-0.20.2

（重复以上操作到另外两机器） **[配置hadoop]** vim conf/core-site.xml 修改： 

> <configuration> <property> <!-- 用于dfs命令模块中指定默认的文件系统协议 --> <name>fs.default.name</name> <value>hdfs://hadoop1:9000</value> </property> </configuration>

vim conf/hdfs-site.xml 修改： 

> <configuration> <property> <!-- DFS中存储文件命名空间信息的目录 --> <name>dfs.name.dir</name> <value>/opt/hadoop/data/dfs.name.dir</value> </property> <property> <!-- DFS中存储文件数据的目录 --> <name>dfs.data.dir</name> <value>/opt/hadoop/data/dfs.data.dir</value> </property> <property> <!-- 是否对DFS中的文件进行权限控制(测试中一般用false)--> <name>dfs.permissions</name> <value>false</value> </property> </configuration>

vim conf/mapred-site.xml 修改： 

> <configuration> <property> <!-- 用来作JobTracker的节点的(一般与NameNode保持一致) --> <name>mapred.job.tracker</name> <value>hadoop1:9001</value> </property> <property> <!-- map/reduce的系统目录（使用的HDFS的路径） --> <name>mapred.system.dir</name> <value>/opt/hadoop/system/mapred.system.dir</value> </property> <property> <!-- map/reduce的临时目录（可使用“,”隔开，设置多重路径来分摊磁盘IO） --> <name>mapred.local.dir</name> <value>/opt/hadoop/data/mapred.local.dir</value> </property> </configuration>

vim masters 

> hadoop1

vim slaves 

> hadoop2 hadoop3

scp conf/* hadoop2:/opt/soft/hadoop-0.20.2/conf/ scp conf/* hadoop3:/opt/soft/hadoop-0.20.2/conf/ ** [初始化]**

> cd $HADOOP_HOME/bin ./hadoop namenode -format

启动 ./start-all.sh **[验证]** $HADOOP_HOME/bin/hadoop dfs -ls / 打开 http://192.168.100.52:50030 http://192.168.100.52:50070 ** [搭建hive集群]** 下载 只需要在hadoop1机器上安装 

> cd /opt/soft/hadoop-0.20.2 wget http://mirror.bjtu.edu.cn/apache/hive/hive-0.7.0/hive-0.7.0.tar.gz tar zxvf hive-0.7.0.tar.gz cd hive-0.7.0 vim ~/.bashrc export HIVE_HOME=/opt/soft/hadoop-0.20.2/hive-0.7.0 $HIVE_HOME/bin/hive >create table tt(id int,name string) row format delimited fields terminated by ',' collection items terminated by "\n" stored as textfile; >select * from tt; >drop table tt;

试玩结束。 **[配置hive]** 准备mysql:hadoop1 user:hadoop pwd:hadoop 

> >create database hive >GRANT all ON hive.* TO hadoop@% IDENTIFIED BY 'hadoop'; >FLUSH PRIVILEGES ;

vim $HIVE_HOME/conf/hive-site.xml 

> <?xml version="1.0"?> <?xml-stylesheet type="text/xsl" href="configuration.xsl"?> <configuration> <property> <name>hive.metastore.local</name> <value>true</value> </property> <property> <name>javax.jdo.option.ConnectionURL</name> <value>jdbc:mysql://hadoop1:3306/hive?createDatabaseIfNotExist=true</value> </property> <property> <name>javax.jdo.option.ConnectionDriverName</name> <value>com.mysql.jdbc.Driver</value> </property> <property> <name>javax.jdo.option.ConnectionUserName</name> <value>hadoop</value> </property> <property> <name>javax.jdo.option.ConnectionPassword</name> <value>hadoop</value> </property> </configuration>

**[启动]** 复制一个mysql-connector-java-5.1.10.jar到hive/lib下后： 

> $HIVE_HOME/bin/hive >create table tt(id int,name string) row format delimited fields terminated by ',' collection items terminated by "\n" stored as textfile;

如果报如下错： 

> FAILED: Error in metadata: javax.jdo.JDOException: Couldnt obtain a new sequence (unique id) : Binary logging not possible. Message: Transaction level 'READ-COMMITTED' in InnoDB is not safe for binlog mode 'STATEMENT'

退出hive后，以root进入mysql执行： 

> >set global binlog_format='MIXED';

这是mysql的一个bug。 安装结束。

## Comments

**[腾飞](#13541 "2011-05-11 00:38:09"):** 详细

**[54chen](#13668 "2011-07-15 11:33:09"):** 有空多多交流

**[张明烁](#13667 "2011-07-14 13:18:02"):** 赞一个，小米还真搭云后台？以后有机会多像你请教。

**[newitfarmer](#13481 "2011-04-02 16:52:22"):** 赞一个，很详细

