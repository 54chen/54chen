title: 类似google big table的tokyo cabinet研究记录
link: http://www.54chen.com/architecture/google-big-table-similar-to-the-tokyo-cabinet-research-notes.html
author: cc0cc
description: 
post_id: 680
created: 2009/07/30 17:45:38
created_gmt: 2009/07/30 09:45:38
comment_status: open
post_name: google-big-table-similar-to-the-tokyo-cabinet-research-notes
status: publish
post_type: post

# 类似google big table的tokyo cabinet研究记录

Tokyo Cabinet是日本人开发的一款数据库，它的功能比较简单，只能键值保存，没有检索功能，以hash table、b+tree、fixed-length array保存。功能类似google的Bigtable的东东。 这套Tokyo系列有三个产品，[Cabinet](http://tokyocabinet.sourceforge.net/index.html)是数据库，[Tyrant](http://tokyocabinet.sourceforge.net/tyrantpkg/)提供管理Cabinet的接口，[Dystopia](http://tokyocabinet.sourceforge.net/dystopiadoc/)提供全文索引。我把Cabinet理解为存储引擎，Tyrant类似mysql的管理器，Dystopia则是插件。 Tokyo Cabinet有如下特点： 

  * 键值保存数据库

  * 数据文件小

  * 高性能，插入1百万记录只需0.4秒(250万 rps),查询1百万记录只需0.3秒(300万 rps)

  * 高并发，支持多线程，读写支持锁记录

  * 使用简单，通过memcached客户端直接使用（需Tyrant)

  * 支持64位架构，容量大

  * 支持事务

Tokyo Tyrant提供管理Cabinet的接口，支持memcached协议，所以，可以通过memcached客户端连接Cabinet。 Tokyo Tyrant有如下特点： 
  * 提供使用Cabinet的接口

  * 支持通过memcached和http协议连接

  * 高并发，查询100万记录17.2秒（5.8万 rps)

  * 支持热备份，复制功能，主持主主(可读写）和主从（分写和读）方式

Tokyo Dystopia是一个全文检索系统,你可以搜索包含某短语的一系列记录，它的特性如下: 
  * 搜索的高性能。

  * 目标文标的高可靠性

  * N-gram模型的高召回率

  * 短语匹配，前缀匹配,后缀匹配[搜索](/601-%e5%8e%9f%e5%88%9b%e4%bd%bf%e7%94%a8postgresql%e6%90%ad%e5%bb%ba%e6%af%94lucene%e6%96%b9%e4%be%bfn%e5%80%8d%e7%9a%84%e5%85%a8%e6%96%87%e6%90%9c%e7%b4%a2-%e7%ac%ac%e4%b8%80%e9%83%a8%e5%88%86/).

  * Unicode下多语种支持。

  * API的分层[架构](/539-%e5%8e%9f%e5%88%9b%e6%95%b4%e7%90%86%e6%a0%a1%e5%86%85%e7%bd%91cto%e9%bb%84%e6%99%b6%e8%ae%b2%e8%bf%b0%e7%bd%91%e7%ab%99%e6%9e%b6%e6%9e%84%e5%8f%98%e8%bf%81-54chen%e5%9b%9e%e5%bf%86%e7%89%88/)支持。

cabinet的文档: http://tokyocabinet.sourceforge.net/spex-en.html tyrant的文档: http://tokyocabinet.sourceforge.net/tyrantdoc/ cabinet和tyrant的ppt幻灯片介绍: http://tokyocabinet.sourceforge.net/tokyoproducts.pdf 可关注下其中的TCTDB,就是对table database的[支持](/591-twitter-api-%e4%b8%ad%e6%96%87%e6%96%87%e6%a1%a3-%e5%89%8d%e8%a8%80/) 使用哪种存储方式,取决于tyrant启动时指定的存储文件名称,table database的文件后缀是.tct