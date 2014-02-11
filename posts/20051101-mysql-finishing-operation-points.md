title: MySql操作要点整理
link: http://www.54chen.com/php-tech/mysql-finishing-operation-points.html
author: admin
description: 
post_id: 19
created: 2005/11/01 22:42:21
created_gmt: 2005/11/01 14:42:21
comment_status: open
post_name: mysql-finishing-operation-points
status: publish
post_type: post

# MySql操作要点整理

列类型 需要的存储量 TINYINT 1 字节 SMALLINT 2 个字节 MEDIUMINT 3 个字节 INT 4 个字节 INTEGER 4 个字节 BIGINT 8 个字节 FLOAT(X) 4 如果 X < = 24 或 8 如果 25 < = X < = 53 FLOAT 4 个字节 DOUBLE 8 个字节 DOUBLE PRECISION 8 个字节 REAL 8 个字节 DECIMAL(M,D) M字节(D+2 , 如果M < D) NUMERIC(M,D) M字节(D+2 , 如果M < D)   日期和时间类型 列类型 需要的存储量 DATE 3 个字节 DATETIME 8 个字节 TIMESTAMP 4 个字节 TIME 3 个字节 YEAR 1 字节 串类型 列类型 需要的存储量 CHAR(M) M字节，1 <= M <= 255 VARCHAR(M) L+1 字节, 在此L <= M和1 <= M <= 255 TINYBLOB, TINYTEXT L+1 字节, 在此L< 2 ^ 8 BLOB, TEXT L+2 字节, 在此L< 2 ^ 16 MEDIUMBLOB, MEDIUMTEXT L+3 字节, 在此L< 2 ^ 24 LONGBLOB, LONGTEXT L+4 字节, 在此L< 2 ^ 32 ENUM('value1','value2',...) 1 或 2 个字节, 取决于枚举值的数目(最大值65535） SET('value1','value2',...) 1，2，3，4或8个字节, 取决于集合成员的数量(最多64个成员） mysql不区分大小写！ 1.#service mysqld start 启动mysql 2.(打mysql进入后)show databases; 显示所有库 3.use(或connect) 库名 使用此数据库 4.show tables; 显示所有表 5.GRANT ALL ON 库名.* TO 用户名; root可执行，给不同的数据库不同的用户管理 6.CREATE DATABASE 新库名; 新建一个数据库 7.quit 退出mysql,另外的参见sql/9 mysqladmin -uroot password "youpassword" 修改管理员密码 GRANT ALL 　　ON 库名.* 　　TO user IDENTIFIEDBY "password" 　　WITH GRANT OPTION; WITH GRANT OPTION 子句是可选的。如果包含该子句，该用户可以将GRANT 语句授予的任何权限授予其他的用户。可以使用该子句将授权的能力授予其他的用户。 GRANT ALL 　　ON 库名.* 　　TO user IDENTIFIEDBY "password" 　　WITH GRANT OPTION; WITH GRANT OPTION 子句是可选的。如果包含该子句，该用户可以将GRANT 语句授予的任何权限授予其他的用户。可以使用该子句将授权的能力授予其他的用户。 撤权并删除用户 要取消一个用户的权限，使用REVOKE语句。REVOKE的语法非常类似于GRANT语句，除了TO用FROM取代并且没有INDETIFED BY和WITH GRANT OPTION子句： ============================================ REVOKE privileges (columns) ON what FROM user ============================================ user部分必须匹配原来GRANT语句的你想撤权的用户的user部分。privileges部分不需匹配，你可以用GRANT语句授权，然后用REVOKE语句只撤销部分权限。 ============================================ REVOKE语句只删除权限，而不删除用户。即使你撤销了所有权限，在user表中的用户记录依然保留，这意味着用户仍然可以连接服务器。要完全删除一个用户，你必须用一条DELETE语句明确从user表中删除用户记录： ============================================ %mysql -u root mysqlmysql＞DELETE FROM user -＞WHERE User="user_name" and Host="host_name";mysql＞FLUSH PRIVILEGES;　 ============================================ DELETE语句删除用户记录，而FLUSH语句告诉服务器重载授权表。（当你使用GRANT和REVOKE语句时，表自动重载，而你直接修改授权表时不是. ============================================

## Comments

**[J](#12279 "2010-05-13 20:34:20"):** 从第一页 看到这是最后一篇 2005年，也快5年了，一路走来 祝 工作顺利 心情愉快 没什么了

