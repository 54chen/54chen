title: 用Sphinx快速搭建站内搜索功能
link: http://www.54chen.com/_linux_/sphinx-build-stations-with-fast-search-function.html
author: 54chen
description: 
post_id: 1220
created: 2010/06/22 15:40:11
created_gmt: 2010/06/22 07:40:11
comment_status: open
post_name: sphinx-build-stations-with-fast-search-function
status: publish
post_type: post

# 用Sphinx快速搭建站内搜索功能

Sphinx[英] [sfɪŋks] ![](http://www.iciba.com/images/orange_result/sound.gif) [美] [sfɪŋks] ![](http://www.iciba.com/images/orange_result/sound.gif) 出自俄罗斯的开源全文搜索引擎软件Sphinx，单一索引最大可包含1亿条记录，在1千万条记录情况下的查询速度为0.x秒（毫秒级）。Sphinx创建索引的速度为：创建100万条记录的索引只需3～4分钟，创建1000万条记录的索引可以在50分钟内完成，而只包含最新10万条记录的增量索引，重建一次只需几十秒。 Sphinx 是一个基于 GPL 2 协议颁发的免费开源的全文搜索引擎.它是专门为更好的整合脚本语言和SQL数据库而设计的.当前内置的数据源支持直接从连接到的 MySQL 或 PostgreSQL 获取数据, 或者你可以使用 XML 通道结构(XML pipe mechanism , 一种基于 Sphinx 可识别的特殊xml格式的索引通道) 下面发出操作记录： 

> 10106 wget http://www.coreseek.cn/uploads/csft/3.2/csft-3.2.12.tar.gz 10107 wget http://www.coreseek.cn/uploads/csft/3.2/mmseg-3.2.12.tar.gz 10109 tar -zxvf mmseg-3.2.12.tar.gz 10110 tar -zxvf csft-3.2.12.tar.gz 10111 cd mmseg-3.2.12 10112 yum -y install glibc-common libtool autoconf automake mysql-devel expat-devel 10113 aclocal 10114 libtoolize --force 10115 automake --add-missing 10116 autoconf 10117 autoheader 10118 ./configure --prefix=/usr/local/mmseg3 10119 make 10120 make install 10121 cp -f src/*/*.h /usr/local/mmseg3/include/mmseg/ 10122 cd .. 10123 ls 10124 cd csft-3.2.12 10125 aclocal 10126 libtoolize --force 10127 automake --add-missing 10128 autoconf 10129 autoheader 10130 perl -pi -e 's/lpthread/lpthread -liconv/g' src/Makefile* 10131 ./configure --prefix=/usr/local/coreseek --enable-id64 --without-python --with-mysql --with-mmseg --with-mmseg-includes=/usr/local/mmseg3/include/mmseg/ --with-mmseg-libs=/usr/local/mmseg3/lib/ 10132 make

make后出现iconv错误，所以修改configure文件，查找到#define USE_LIBICONV 把1改为0 重新执行configure和make 

> 10157 make install 10158 cd /usr/local/coreseek/etc/ 10164 cp sphinx.conf.dist csft.conf 10165 vim csft.conf 10169 mysql -uroot test 10173 touch /data/exceptions.txt 10174 bin/indexer --all 10177 bin/search test

下期将推出中文搜索测试以及分布式方案。 \---华丽的分隔线--- 向北京本地喜爱看电影的哥推荐： ![](http://img03.taobaocdn.com/imgextra/i3/T1tXFEXlFoXXbiWc79_073023.jpg) 昨天哥去UME华星提前看了电影 80后 ，严格的说，这电影和80后没有太多关系，故事还是很感人的，适合带MM看。

## Comments

**[54dev](#13406 "2011-03-11 17:24:46"):** 80后刚看完。。。情节有点乱

**[游目网](#12463 "2010-06-25 12:24:50"):** 非常不错的东西，在网站的发展过程中会用的着的。

**[丽水生活网](#12466 "2010-06-28 12:45:38"):** 一直就在关注sphinx和全文搜索coreseek，还没用上呢~~~ 分布式设计很重要啊，不然一般大的网站都不止千万数据的吧~

**[liqwei](#12511 "2010-07-15 12:01:10"):** 支持中文分词吗？效果如何？这是在中文网站上使用的关键啊！

