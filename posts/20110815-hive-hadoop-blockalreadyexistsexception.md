title: 记hadoop故障一例：BlockAlreadyExistsException
link: http://www.54chen.com/java-ee/hive-hadoop-blockalreadyexistsexception.html
author: 54chen
description: 
post_id: 1791
created: 2011/08/15 21:17:55
created_gmt: 2011/08/15 13:17:55
comment_status: open
post_name: hive-hadoop-blockalreadyexistsexception
status: publish
post_type: post

# 记hadoop故障一例：BlockAlreadyExistsException

![](http://img02.taobaocdn.com/imgextra/i2/T116J6XhxoXXcdrwMU_014654.jpg) hive版本：0.7.0 hadoop版本：0.20.2 在线上跑了一个季度了，基本上没什么问题，今天突然出了问题。 在hive执行时留下的蛛丝马迹： 

> Failed with exception org.apache.hadoop.hdfs.server.namenode.NotReplicatedYetException: Not replicated yet:/tmp/hive-root/hive_2011-08-15_00-31-02_332_247809173824307798/-ext-10000/access_bucket-2011-08-14_00004 at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.getAdditionalBlock(FSNamesystem.java:1257) at org.apache.hadoop.hdfs.server.namenode.NameNode.addBlock(NameNode.java:422) at sun.reflect.GeneratedMethodAccessor2037.invoke(Unknown Source) at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25) at java.lang.reflect.Method.invoke(Method.java:597) at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:508) at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:959) at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:955) at java.security.AccessController.doPrivileged(Native Method) at javax.security.auth.Subject.doAs(Subject.java:396) at org.apache.hadoop.ipc.Server$Handler.run(Server.java:953) FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.CopyTask

在DN中发现： 

> 2011-08-15 00:47:09,138 INFO org.apache.hadoop.hdfs.server.datanode.DataNode: writeBlock blk_8964076545845199727_216399 received exception org.apache.hadoop.hdfs.server.datanode.BlockAlreadyExistsException: Block blk_8964076545845199727_216399 is valid, and cannot be written to. 2011-08-15 00:47:09,138 ERROR org.apache.hadoop.hdfs.server.datanode.DataNode: DatanodeRegistration(192.168.1.23:50010, storageID=DS-52195649-192.168.1.23-50010-1299427987620, infoPort=50075, ipcPort=50020):DataXceiver org.apache.hadoop.hdfs.server.datanode.BlockAlreadyExistsException: Block blk_8964076545845199727_216399 is valid, and cannot be written to. at org.apache.hadoop.hdfs.server.datanode.FSDataset.writeToBlock(FSDataset.java:983) at org.apache.hadoop.hdfs.server.datanode.BlockReceiver.(BlockReceiver.java:98) at org.apache.hadoop.hdfs.server.datanode.DataXceiver.writeBlock(DataXceiver.java:259) at org.apache.hadoop.hdfs.server.datanode.DataXceiver.run(DataXceiver.java:103) at java.lang.Thread.run(Thread.java:662) 2011-08-15 00:47:15,366 WARN org.apache.hadoop.util.Shell: Could not get disk usage information org.apache.hadoop.util.Shell$ExitCodeException: du: cannot access `/data/hadoop/data/dfs.data.dir/tmp/blk_-1540848236479330018_216371.meta': No such file or directory du: cannot access `/data/hadoop/data/dfs.data.dir/tmp/blk_-1540848236479330018': No such file or directory at org.apache.hadoop.util.Shell.runCommand(Shell.java:195) at org.apache.hadoop.util.Shell.run(Shell.java:134) at org.apache.hadoop.fs.DU.access$200(DU.java:29) at org.apache.hadoop.fs.DU$DURefreshThread.run(DU.java:84) at java.lang.Thread.run(Thread.java:662)

看着像是DN写入的时候遇到了服务不响应，google追了一把，发现DN上全都忘记设置ulimit了，汗： ulimit -SHn 18912 **参考：** http://www.cloudera.com/blog/2009/03/configuration-parameters-what-can-you-just-ignore/ http://www.michael-noll.com/blog/2011/04/09/benchmarking-and-stress-testing-an-hadoop-cluster-with-terasort-testdfsio-nnbench-mrbench/ http://sudhirvn.blogspot.com/2010/07/hadoop-error-logs-orgapachehadoophdfsse.html