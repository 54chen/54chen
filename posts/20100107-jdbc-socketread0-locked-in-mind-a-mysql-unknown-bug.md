title: jdbc socketRead0 locked 记一个Mysql的不明bug
link: http://www.54chen.com/java-ee/jdbc-socketread0-locked-in-mind-a-mysql-unknown-bug.html
author: cc0cc
description: 
post_id: 922
created: 2010/01/07 12:10:57
created_gmt: 2010/01/07 04:10:57
comment_status: open
post_name: jdbc-socketread0-locked-in-mind-a-mysql-unknown-bug
status: publish
post_type: post

# jdbc socketRead0 locked 记一个Mysql的不明bug

在开发人人网海量存储系统Nuclear的过程中，使用到[mysql](/php-tech/mysql%e7%9a%84%e6%80%a7%e8%83%bd%e8%b0%83%e4%bc%98%e5%b7%a5%e5%85%b7%ef%bc%9a%e6%af%94mysqlreport%e6%9b%b4%e6%96%b9%e4%be%bf%e7%9a%84tuning-primersh.html)引擎，用了spring+dbcp+jdbc，在压力测试的过程中出现了问题。 在追踪java stack的过程中发现如下的问题： 

> java.lang.Thread.State: RUNNABLE at java.net.SocketInputStream.socketRead0(Native Method) at java.net.SocketInputStream.read(SocketInputStream.java:129) at com.mysql.jdbc.util.ReadAheadInputStream.fill(ReadAheadInputStream.java:113) at com.mysql.jdbc.util.ReadAheadInputStream.readFromUnderlyingStreamIfNecessary(ReadAheadInputStream.java:160) at com.mysql.jdbc.util.ReadAheadInputStream.read(ReadAheadInputStream.java:188) \- locked <0x00002aaab9b2b0d8> (a com.mysql.jdbc.util.ReadAheadInputStream) at com.mysql.jdbc.MysqlIO.readFully(MysqlIO.java:2494) at com.mysql.jdbc.MysqlIO.reuseAndReadPacket(MysqlIO.java:3005) at com.mysql.jdbc.MysqlIO.reuseAndReadPacket(MysqlIO.java:2938) at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3481) at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:919) at com.mysql.jdbc.MysqlIO.nextRow(MysqlIO.java:1416) at com.mysql.jdbc.MysqlIO.readSingleRowSet(MysqlIO.java:2883) at com.mysql.jdbc.MysqlIO.getResultSet(MysqlIO.java:476) at com.mysql.jdbc.MysqlIO.readResultsForQueryOrUpdate(MysqlIO.java:2576) at com.mysql.jdbc.MysqlIO.readAllResults(MysqlIO.java:1757) at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:2167) at com.mysql.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:2648) \- locked <0x00002aaab9d98790> (a java.lang.Object) at com.mysql.jdbc.PreparedStatement.executeInternal(PreparedStatement.java:2077) at com.mysql.jdbc.PreparedStatement.executeQuery(PreparedStatement.java:2228) \- locked <0x00002aaab9d98790> (a java.lang.Object) at org.apache.commons.dbcp.DelegatingPreparedStatement.executeQuery(DelegatingPreparedStatement.java:93) at org.apache.commons.dbcp.DelegatingPreparedStatement.executeQuery(DelegatingPreparedStatement.java:93) at org.springframework.jdbc.core.JdbcTemplate$1.doInPreparedStatement(JdbcTemplate.java:648) at org.springframework.jdbc.core.JdbcTemplate.execute(JdbcTemplate.java:591) at org.springframework.jdbc.core.JdbcTemplate.query(JdbcTemplate.java:641) at org.springframework.jdbc.core.JdbcTemplate.query(JdbcTemplate.java:670) at org.springframework.jdbc.core.JdbcTemplate.query(JdbcTemplate.java:702) at com.renren.nuclear.storage.MySqlDumpEngine.getListByLeftRight(MySqlDumpEngine.java:198)

其中锁住了[tcp](/memo/%e4%b8%80%e5%91%a8%e5%ba%9f%e8%af%9d%e6%b1%87%e6%80%bb%e3%80%9054chen-twitter-2009-08-22%e3%80%91.html)的socket，在研究无果的情况下，试着搜了一下，发现已经有人在[mysql ](http://www.54chen.com/architecture/wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes.html)bugs里提到： http://bugs.mysql.com/bug.php?id=9515 **分析产生bug的原因：** [mysql](http://www.54chen.com/php-tech/mysql%e7%9a%84%e6%80%a7%e8%83%bd%e8%b0%83%e4%bc%98%e5%b7%a5%e5%85%b7%ef%bc%9a%e6%af%94mysqlreport%e6%9b%b4%e6%96%b9%e4%be%bf%e7%9a%84tuning-primersh.html)建表的时候使用[innoDB](http://www.54chen.com/_linux_/%e5%8e%9f%e5%88%9b%e5%ae%9e%e6%b5%8bmysql%e6%97%b6%e9%97%b4%e5%ad%97%e6%ae%b5%e7%a9%b6%e7%ab%9f%e4%bd%bf%e7%94%a8int%e8%bf%98%e6%98%afdatetime%ef%bc%9f.html)，表中有过千万的数据，每次取过多的数据，就会重现。 可能的原因：jdbc在做网络层的时候tcp的buffer不够所致io锁。 **最简单的解决办法：** 把原来的limit 1000改小，变成了limit 100。

## Comments

**[Tang](#15008 "2012-08-25 14:39:22"):** 弱弱的问一下： 把原来的limit 1000改小，变成了limit 100 是指修改什么的limit？具体是那个参数？

**[54chen](#15009 "2012-08-27 11:03:46"):** 是指你sql中的limit 一次取数据的量。

**[Eric](#13203 "2010-11-16 11:24:21"):** 这个问题貌似很麻烦。 现在公司现网出现频繁。 关键是没有查询大量数据。

**[Jack](#12109 "2010-01-08 14:16:28"):** 博主的文章非常专业，我的网站是软件测试类的，刚做不久但会坚持做，我已经做好了博主的友情链接，希望能交换。 网站名： www.test21.cn Thanks

**[cc0cc](#12110 "2010-01-08 20:28:32"):** 对不起，只加认识的人

**[isll](#12119 "2010-01-15 16:36:07"):** nuclear跟cassandra区别大吗？ 不是说有可能开源吗？有大概时间？ ：）

**[cc0cc](#12120 "2010-01-15 16:57:18"):** 和cassandra算是同胞兄弟，不过研究过cassandra的代码，没有nuclear切换引擎方便。开源时间等大佬们批，不过会开源的：）

**[isll](#12121 "2010-01-15 17:20:17"):** 期待，：）

**[cc0cc](#12125 "2010-01-18 19:41:00"):** 很快会出来的：）

