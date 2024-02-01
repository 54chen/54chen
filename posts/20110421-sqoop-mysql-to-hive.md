title: 利用sqoop将mysql数据同步到hive手记
link: http://www.54chen.com/java-ee/sqoop-mysql-to-hive.html
author: 54chen
description: 
post_id: 1686
created: 2011/04/21 15:28:01
created_gmt: 2011/04/21 07:28:01
comment_status: open
post_name: sqoop-mysql-to-hive
status: publish
post_type: post

# 利用sqoop将mysql数据同步到hive手记

![sqoop mysql hive](http://img01.taobaocdn.com/imgextra/i1/T14rp.XdVmXXa7cv.Z_032332.jpg) 1.下载 http://archive.cloudera.com/cdh/3/sqoop-1.2.0-CDH3B4.tar.gz 2.下载 http://archive.cloudera.com/cdh/3/hadoop-0.20.2-CDH3B4.tar.gz 3.解压 2 4.复制3里hadoop-core-0.20.2-CDH3B4.jar到sqoop的lib下 5.在某处复制mysql-connector-java-5.1.10.jar到sqoop的lib下 6.修改configure-sqoop 

> 注释掉hbase zookeeper检查： #if [ ! -d "${HBASE_HOME}" ]; then # echo "Error: $HBASE_HOME does not exist!" # echo 'Please set $HBASE_HOME to the root of your HBase installation.' # exit 1 #fi #if [ ! -d "${ZOOKEEPER_HOME}" ]; then # echo "Error: $ZOOKEEPER_HOME does not exist!" # echo 'Please set $ZOOKEEPER_HOME to the root of your ZooKeeper installation.' # exit 1 #fi

7.运行： 列出mysql所有的表： 

> ./sqoop list-tables --connect jdbc:mysql://127.0.0.1/operation --username root --password 123

导入mysql表到hive： 

> ./sqoop import --connect jdbc:mysql://192.168.100.52/operation --username root --password 12345678 --table active_uuid --hive-import

导入需要表里有主建，还要注意不要使用127.0.0.1，因为map出去不一定在哪个节点执行。 如果曾经执行失败过，那再执行的时候，会有错误提示： 

> ERROR tool.ImportTool: Encountered IOException running import job: org.apache.hadoop.mapred.FileAlreadyExistsException: Output directory xxx already exists

执行 $HADOOP_HOME/bin/hadoop fs -rmr xxx 即可 8.验证: 

> bin/hive show tables;多了一个表

9.经验： sqoop做了一些mysqldump时的map reduce，所以速度会比单纯的dump后load快。

## Comments

**[adrian](#13734 "2011-08-10 17:17:28"):** tinyint 导入hbase成为了bool值，这个如何处理呢？

