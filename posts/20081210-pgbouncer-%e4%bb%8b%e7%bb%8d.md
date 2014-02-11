title: Pgbouncer 介绍
link: http://www.54chen.com/life/pgbouncer-%e4%bb%8b%e7%bb%8d.html
author: cc0cc
description: 
post_id: 247
created: 2008/12/10 10:35:49
created_gmt: 2008/12/10 02:35:49
comment_status: open
post_name: pgbouncer-%e4%bb%8b%e7%bb%8d
status: private
post_type: post

# Pgbouncer 介绍

## PgBouncer

## 目录

[[隐藏](javascript:toggleToc\(\))]

  * [1 PgBouncer](http://10.62.164.49/wiki/index.php/Pgbouncer_%E4%BB%8B%E7%BB%8D#PgBouncer)
    * [1.1 特性](http://10.62.164.49/wiki/index.php/Pgbouncer_%E4%BB%8B%E7%BB%8D#.E7.89.B9.E6.80.A7)
    * [1.2 文档](http://10.62.164.49/wiki/index.php/Pgbouncer_%E4%BB%8B%E7%BB%8D#.E6.96.87.E6.A1.A3)
    * [1.3 连接池模式和 SQL 特性的映射](http://10.62.164.49/wiki/index.php/Pgbouncer_%E4%BB%8B%E7%BB%8D#.E8.BF.9E.E6.8E.A5.E6.B1.A0.E6.A8.A1.E5.BC.8F.E5.92.8C_SQL_.E7.89.B9.E6.80.A7.E7.9A.84.E6.98.A0.E5.B0.84)

**PostgreSQL 的轻量的连接池。**

### [[编辑](http://10.62.164.49/wiki/index.php?title=Pgbouncer_%E4%BB%8B%E7%BB%8D&action=edit&section=2)] 特性

  * 在轮转连接的时候有好几种方式：

    **Session pooling/会话连接池**

    最礼貌的方法。在客户端连接的时候，在它的连接生命期内，会给它赋予一个服务器连接。在客户端断开的时候，服务器连接会放回到连接池中。 

    **Transaction pooling/事务连接池**

    服务器连接只有在一个事务里的时候才赋予客户端。在 PgBouncer 注意到事务结束的时候，服务器将会放回连接池中。这是一个 hack，因为它打破了应用对后段连接的看法。只有在应用配合这样的使用模式，没有使用会破坏这种使用模式的时候才能用这个连接方式。参阅下标获取会破坏这种模式的特性。 

    **Statement pooling/语句连接池**

    最激进的模式。这是事务连接池的一个扭曲的变种 - 不允许多语句的事务。这就意味着是在客户端强制“autocomit”模式，主要是给 PL/Proxy 用的。 

  * 内存需求低（缺省的时候每个连接 2k）。这事因为 PgBouncer 不需要一次就看到完整的包。
  * 它不是和单个后端服务器绑定的，目标数据库可以位于不同的主机上。
  * 对大多数设置，都支持在线的重新配置，无需重启。
  * 支持在线的重启/升级，而不会退出客户端的连接。
  * 只支持协议 V3 版本，因此后段版本必须 >= 7.4。

### [[编辑](http://10.62.164.49/wiki/index.php?title=Pgbouncer_%E4%BB%8B%E7%BB%8D&action=edit&section=3)] 文档

  * [Pgbouncer 用户手册](http://10.62.164.49/wiki/index.php/Pgbouncer_%E7%94%A8%E6%88%B7%E6%89%8B%E5%86%8C)
  * [Pgbouncer 管理员手册](http://10.62.164.49/wiki/index.php/Pgbouncer_%E7%AE%A1%E7%90%86%E5%91%98%E6%89%8B%E5%86%8C)

### [[编辑](http://10.62.164.49/wiki/index.php?title=Pgbouncer_%E4%BB%8B%E7%BB%8D&action=edit&section=4)] 连接池模式和 SQL 特性的映射

下面的表列出了各种 PostgreSQL 特性，以及他们是否和 PgBouncer 连接池模式兼容的对应关系。请注意**事务连接池**打破了客户端对服务器的预期，只能在应用配合并且不使用那些会破坏该模式的特性的时候才能使用。 

特性
会话连接池
事务连接池

启动参数
支持 [0]
支持 [0]

SET/RESET
支持
从不支持

LISTEN/NOTIFY
支持
从不支持

WITHOUT HOLD CURSOR
支持
支持

WITH HOLD CURSOR
支持 [1]
从不支持

协议级别的准备好的规划
支持 [1]
不支持 [2]

PREPARE / DEALLOCATE
支持 [1]
从不支持

ON COMMIT DROP 临时表
支持
支持

PRESERVE/DELETE ROWS 临时表
支持 [1]
从不支持

重置缓存的规划
支持 [1]
支持 [1]

LOAD 语句
支持
从不支持

  * [0] - 启动参数是： client_encoding，datestyle，timezone 和 standard_conforming_strings。PgBouncer 会判断这些参数的变化并且它能保证对客户端来说是一致的。从 PgBouncer 1.1 开始可用。
  * [1] - 完全透明要求 PostgreSQL 8.3 和 PgBouncer 1.1，并且还要设置 server_reset_query = DISCARD ALL
  * [2] - 我们可以在 PgBouncer 里添加那部分支持。

取自"<http://10.62.164.49/wiki/index.php/Pgbouncer_%E4%BB%8B%E7%BB%8D>"