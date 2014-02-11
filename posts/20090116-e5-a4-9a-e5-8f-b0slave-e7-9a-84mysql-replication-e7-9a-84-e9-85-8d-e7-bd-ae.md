title: 多台slave的mysql replication的配置
link: http://www.54chen.com/_linux_/e5-a4-9a-e5-8f-b0slave-e7-9a-84mysql-replication-e7-9a-84-e9-85-8d-e7-bd-ae.html
author: cc0cc
description: 
post_id: 406
created: 2009/01/16 10:51:14
created_gmt: 2009/01/16 02:51:14
comment_status: closed
post_name: e5-a4-9a-e5-8f-b0slave-e7-9a-84mysql-replication-e7-9a-84-e9-85-8d-e7-bd-ae
status: publish
post_type: post

# 多台slave的mysql replication的配置

5.1.24版本的配置请看：http://www.masalife.com/archives/173 

** ** 一、先修改服务器的配置文件        1、Master服务器配置简单，修改my.cnf为:        server-id       = 1 log-bin set-variable=binlog-ignore-db=mysql 2、slave1的配置加入 server-id       = 2 master-host = 172.16.20.135 master-user = rep master-password = cnrep master-port = 3306 log-bin set-variable=replicate-ignore-db=mysql set-variable=replicate-do-db=AliSMS set-variable=replicate-do-db=lcd set-variable=replicate-do-db=loginmanager set-variable=replicate-do-db=samis set-variable=replicate-do-db=sareport set-variable=replicate-do-db=syslog set-variable=replicate-do-db=web_speed log-slave-updates 3、slave2服务器的配置 server-id       = 3 master-host = 172.16.20.3 master-user = rep1 master-password = cnrep master-port = 3306 set-variable=replicate-ignore-db=mysql set-variable=replicate-do-db=AliSMS set-variable=replicate-do-db=lcd set-variable=replicate-do-db=loginmanager set-variable=replicate-do-db=samis set-variable=replicate-do-db=sareport set-variable=replicate-do-db=syslog set-variable=replicate-do-db=web_speed   二、重启master数据库   三、然后锁定master数据库的表： mysql>FLUSH TABLES WITH READ LOCK; 四、在master数据库中添加用于slave1同步的用户，并赋予相关权限： mysql>GRANT REPLICATION SLAVE ON *.* TO rep@sa_cfengine1 IDENTIFIED BY 'cnrep'; mysql>GRANT FILE,SELECT,REPLICATION SLAVE ON *.* TO rep@sa_cfengine1 IDENTIFIED BY 'cnrep';   五、在slave1数据库中添加用于slave2同步的用户，并赋予相关权限： mysql>GRANT REPLICATION SLAVE ON *.* TO rep1@sa_cfengine2 IDENTIFIED BY 'cnrep'; mysql>GRANT FILE,SELECT,REPLICATION SLAVE ON *.* TO rep1@sa_cfengine2 IDENTIFIED BY 'cnrep';   六、同步数据库： 方法很多，可以打包之后scp，再解压，由于sa_cfengine1到mysql master服务器通道打通了，切sa_cfengine2到sa_cfengine1通道也打了，故直接scp整个数据库目录即可。 注意：此时要注意删除同步过来的日志文件，最好把与数据库无关的文件全删除（可以将非目录的文件全删了）。   七、重启salve1的mysql，起来之后锁定表   八、重启slave2的mysql，然后先后给slave1和master服务器的mysql表解锁 mysql> UNLOCK TABLES；   九、分别登录slave1和slave2的mysql，查看同步状态： 
    
    
            mysql>SHOW SLAVE STATUS\G

*************************** 1. row ***************************              Slave_IO_State: Waiting for master to send event                 Master_Host: 172.16.20.135                 Master_User: rep                 Master_Port: 3306               Connect_Retry: 60             Master_Log_File: mysql-bin.000051         Read_Master_Log_Pos: 13856842              Relay_Log_File: sa_cfengine1-relay-bin.000013               Relay_Log_Pos: 624419       Relay_Master_Log_File: mysql-bin.000051           ** Slave_IO_Running: Yes** **          Slave_SQL_Running: Yes**             Replicate_Do_DB: AliSMS,lcd,loginmanager,samis,sareport,syslog,web_speed         Replicate_Ignore_DB: mysql,mysql          Replicate_Do_Table:      Replicate_Ignore_Table:     Replicate_Wild_Do_Table: Replicate_Wild_Ignore_Table:                  Last_Errno: 0                  Last_Error:                Skip_Counter: 0         Exec_Master_Log_Pos: 13856842             Relay_Log_Space: 624419             Until_Condition: None              Until_Log_File:               Until_Log_Pos: 0          Master_SSL_Allowed: No          Master_SSL_CA_File:          Master_SSL_CA_Path:             Master_SSL_Cert:           Master_SSL_Cipher:              Master_SSL_Key:       Seconds_Behind_Master: 0 1 row in set (0.01 sec) 注意标注为红色的地方，两个都是yes说明一切正常，否则要检查原因，可以看error log查找原因后做相应的处理。   十、测试：        在master数据库中update在同步列表中的一个表的一个字段，如果slave服务器的做相应改变，则测试用过。

## Comments

**[变压器](#140 "2009-01-16 20:52:14"):** 学习中 不错啊

**[花荣](#12488 "2010-07-07 10:16:42"):** 这个。你这是mysql的什么版本。。怎么配置这么奇怪的。。 master-host = 172.16.20.3 master-user = rep1 master-password = cnrep master-port = 3306 这种配置似乎已经过时了。。。 另外你这种同步肯定是有问题的。。。。 可以看一下我写的文章： http://www.masalife.com/archives/173 请自助翻墙。

**[54chen](#12489 "2010-07-07 12:18:49"):** 这个时间很长了，应该是08年的文章了 09年转过来的 不具有参考性 我修改一下把兄弟的连接加到文章当中 以免贻害后人

