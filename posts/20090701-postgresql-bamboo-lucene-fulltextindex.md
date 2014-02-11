title: [原创]使用postgreSQL+bamboo搭建比lucene方便N倍的全文搜索 第一部分
link: http://www.54chen.com/_linux_/postgresql-bamboo-lucene-fulltextindex.html
author: cc0cc
description: 
post_id: 601
created: 2009/07/01 18:06:27
created_gmt: 2009/07/01 10:06:27
comment_status: open
post_name: postgresql-bamboo-lucene-fulltextindex
status: publish
post_type: post

# [原创]使用postgreSQL+bamboo搭建比lucene方便N倍的全文搜索 第一部分

[文章作者：陈臻 本文版本：v1.2 最后修改：2009.7.7 转载请注明原文链接：<http://www.54chen.com/_linux_/postgresql-bamboo-lucene-fulltextindex.html> ] 修正：一些“--”（连续的两个杠）被转成了全角的“-”（一个杠）了，运行不过的试试-变成-- **所有用到到包有：** [cmake](/c/601)-2.6.4.tar.gz (编nlpbamboo用) CRF++-0.53.tar.gz(同上) nlpbamboo-1.1.1.tar.bz2（分词用） postgreSQL-8.3.3.tar.gz（索引用） **安装**[pgsql](http://www.54chen.com/c/101) tar -zxvf postgreSQL-8.3.3.tar.gz cd postgre-8.3.3 
    
    
    ./configure --prefix=/opt/pgsql

make make install useradd postgre chown -R postgre.postgre /opt/pgsql su - postgre vi ~postgre/.bash_profile 添加 export PATH PGLIB=/opt/pgsql/lib PGDATA=/data/PGSearch PATH=$PATH:/opt/pgsql/bin MANPATH=$MANPATH:/opt/pgsql/man export PGLIB PGDATA PATH MANPATH # mkdir -p /data/PGSearch # chown -R postgre.postgre /data/PGSearch # chown -R postgre.postgre /opt/pgsql 
    
    
    #sudo -u postgre /opt/pgsql/bin/initdb --locale=zh_CN.UTF-8 --encoding=utf8 -D /data/PGSearch
    
    
    #sudo -u postgre /opt/pgsql/bin/postmaster -i -D /data/PGSearch &  //允许网络访问

#sudo -u postgre /opt/pgsql/bin/createdb kxgroup # vim /data/PGSearch/pg_hba.conf  如下增加可访问的[机器](/c/264)： host    all         all         10.2.19.178 255.255.255.0               trust #su - postgre $pg_ctl stop $postmaster -i -D /data/PGSearch & **安装中文分词（Cmake CRF++ bambo****o****)** Cmake是为了编译bamboo,CRF++是bamboo依赖的。 tar -zxvf cmake-2.6.4.tar.gz cd cmake-2.6.4 ./configure gmake make install tar -zxvf CRF++-0.53.tar.gz cd CRF++-0.53 ./configure make make install tar -jxvf nlpbamboo-1.1.1.tar.bz2 cd nlpbamboo-1.1.1 mkdir build cd build/ cmake .. -DCMAKE_BUILD_TYPE=release make all make install cp index.tar.bz2 /opt/bamboo/ cd /opt/bamboo/ tar -jxvf index.tar.bz2 #/opt/bamboo/bin/bamboo 如果出现： ERROR: libcrfpp.so.0: cannot open shared object file: No such file or directory 就执行： ln -s /usr/local/lib/libcrfpp.so.* /usr/lib/ ldconfig **增加上中文分词扩展到pgsql** #vim /root/.bash_profile 也增加： PGLIB=/opt/pgsql/lib PGDATA=/data/PGSearch PATH=$PATH:/opt/pgsql/bin MANPATH=$MANPATH:/opt/pgsql/man export PGLIB PGDATA PATH MANPATH #source ~/.bash_profile cd /opt/bamboo/exts/postgres/chinese_parser/ make make install su - postgre cd /opt/pgsql/share/contrib/ touch /opt/pgsql/share/tsearch_data/chinese_utf8.stop psql kxgroup \i chinese_parser.sql 导入 再执行下面的sql，已经可以将一段话分词了： 
    
    
    SELECT to_tsvector('chinesecfg', '结果在命令行下执行bamboo才知道');

先到这里，下一部分讲述对TEXT字段进行索引和查询，完整构造一整个[搜索](/c/591)引擎。

## Comments

**[agentzh](#10639 "2009-07-05 13:59:21"):** Yay! chenzhen++

**[小一](#10641 "2009-07-06 14:48:22"):** 啊啊啊啊啊啊啊 啊啊 好！

**[tsung](#10642 "2009-07-06 14:53:21"):** 楼上几个很..

**[blog.emomos.com](#10643 "2009-07-06 16:54:24"):** 赶紧搞下部分

**[d_yang](#10659 "2009-07-16 22:08:11"):** 我最舍不得就是往sql engine里放插件。 这样，Sphinx SE被我无情的抛弃了。

**[d_yang](#11063 "2009-08-13 22:57:01"):** 在Mac下装psycopg2： export PATH="/Library/PostgreSQL/8.4/bin:$PATH" sudo easy_install psycopg2 Danmit, what planet does this capcha come from

**[cc0cc](#11148 "2009-08-14 17:40:29"):** :)

**[Zoom.Quiet](#12709 "2010-08-17 23:12:56"):** 介个好象是刘鑫的创意哈...

**[54chen](#12723 "2010-08-18 19:08:10"):** hoho

