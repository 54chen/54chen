title: 用hadoop hive协同scribe log用户行为分析方案
link: http://www.54chen.com/architecture/hadoop-hive-scribe-log.html
author: 54chen
description: 
post_id: 1675
created: 2011/04/13 10:46:03
created_gmt: 2011/04/13 02:46:03
comment_status: open
post_name: hadoop-hive-scribe-log
status: publish
post_type: post

# 用hadoop hive协同scribe log用户行为分析方案

![hadoop,hive,scribe](http://img02.taobaocdn.com/imgextra/i2/T1sUV8Xk4sXXablPM9_104155.jpg) scribe 是facebook 开源的分布式日志系统，在其示例配置中，并发量可达到max_msg_per_second=2000000。54chen使用手记见：<http://www.54chen.com/java-ee/log-server-scribe-helper.html> hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供完整的sql查询功能，可以将sql语句转换为 MapReduce任务进行运行。54chen使用手记见：<http://www.54chen.com/_linux_/hive-hadoop-how-to-install.html> 下面来讲述二者合成的使用办法： **创建和scribe格式相符的hive table** bin/hive 

> > create table log(active string,uuid string,ip string,dt string) row format delimited fields terminated by ',' collection items terminated by "\n" stored as textfile;

**加载数据**

> >LOAD DATA LOCAL INPATH '/opt/soft/hadoop-0.20.2/hive-0.7.0/data/log-2011-04-13*' OVERWRITE INTO TABLE log;

查询 

> >select count(*) from log group by uuid;

进入mapreduce计算，过了一会儿，结果出来了。 **修改已经定义数据格式** cutter.py 数据自定义脚本,从标准输入拿到数据后输出到标准输出 cd bin/ ./hive 

> >add file /opt/soft/hadoop-0.20.2/hive-0.7.0/bin/hive-shell/cutter.py; >select transform (active,uuid,ip,dt) using 'python cutter.py' as (active,uuid,ip,dt) from log limit 1;

**得到格式化后的结果**

> >create table log_new(active string,uuid string,ip string,dt string) row format delimited fields terminated by ',' collection items terminated by "\n" stored as textfile; >INSERT OVERWRITE TABLE log_new select transform (active,uuid,ip,dt) using 'python cutter.py' as (active,uuid,ip,time) from log;

**以hive server运行(thrift的server)**

> bin/hive --service hiveserver

默认以thrift service在10000启动服务。 **用标准的thrift-jdbc来连接hive**

> public class HiveJdbcClient { private static String driverName = "org.apache.hadoop.hive.jdbc.HiveDriver"; /** * @param args * @throws SQLException */ public static void main(String[] args) throws SQLException { try { Class.forName(driverName); } catch (ClassNotFoundException e) { e.printStackTrace(); System.exit(1); } Connection con = DriverManager.getConnection("jdbc:hive://192.168.100.52:10000/default", "", ""); Statement stmt = con.createStatement(); ResultSet res = stmt.executeQuery("select count(distinct uuid) from usage_new where active='user_login_succ'"); if (res.next()) { System.out.println(res.getString(1)); } } }

**依赖的jar包(maven pom)**

> <dependency> <groupId>hadoop</groupId> <artifactId>hive-jdbc</artifactId> <version>0.7.0</version> </dependency> <dependency> <groupId>hadoopl</groupId> <artifactId>hive-metastore</artifactId> <version>0.7.0</version> </dependency> <dependency> <groupId>hadoop</groupId> <artifactId>hive-exec</artifactId> <version>0.7.0</version> </dependency> <dependency> <groupId>hadoop</groupId> <artifactId>hive-service</artifactId> <version>0.7.0</version> </dependency> <dependency> <groupId>org.apache.thrift</groupId> <artifactId>thrift</artifactId> <version>0.5.0-xiaomi</version> </dependency> <dependency> <groupId>facebook</groupId> <artifactId>thrift-fb303</artifactId> <version>0.5.0</version> </dependency> <dependency> <groupId>hadoop</groupId> <artifactId>hadoop-core</artifactId> <version>0.20.2</version> </dependency> <dependency> <groupId>xerces</groupId> <artifactId>xercesImpl</artifactId> <version>2.9.1</version> </dependency> <dependency> <groupId>xalan</groupId> <artifactId>xalan</artifactId> <version>2.7.1</version> </dependency>

## Comments

**[wendell](#14283 "2012-01-24 15:29:03"):** 初学，不知道load的文件怎么来的， 请教！

**[小飞](#13507 "2011-04-14 19:24:47"):** ...............................貌似很给力...

**[bandit](#13509 "2011-04-15 22:50:40"):** 这类文章要多多多

