title: 分布式日志系统scribe使用手记
link: http://www.54chen.com/_linux_/log-server-scribe-helper.html
author: 54chen
description: 
post_id: 1617
created: 2011/02/24 11:29:18
created_gmt: 2011/02/24 03:29:18
comment_status: open
post_name: log-server-scribe-helper
status: publish
post_type: post

# 分布式日志系统scribe使用手记

![java,scribe](http://img02.taobaocdn.com/imgextra/i2/T1a5J6XmhtXXbWyPE9_103845.jpg) scribe 是facebook 开源的分布式日志系统 。其使用了thrift传输log，由于使用thrift，所以不论是什么语言的项目都可以实现日志收集，可达到远程或者是本地同步远程的分布式日志收集效果，在其示例配置中，并发量可达到max_msg_per_second=2000000，对普通的应用来说，每秒上百万的访问量可能性很小，因此基本上即使最简单的配置，远程收集所有项目的log也是可靠的，如果压力大的话，可以采取主从的配置，将日志打到本地后由scribe的主从配置来自动同步。下面讲述如何安装和使用scribe。 **下载boost、thrift、scribe**

> boost_1_45_0.tar.gz thrift-0.5.0.tar.gz facebook-scribe-2ee14d3.zip

**通过yum安装需要的依赖**

> yum install libevent libevent-devel python-devel yum install gcc-c++ yum install automake yum install byacc flex 

**安装boost**

> tar -zxvf boost_1_45_0.tar.gz cd boost_1_45_0 ./bootstrap.sh ./bjam install --prefix=/opt/soft/bootstrap

**安装thrift-0.5.0**

> tar zxvf thrift-0.5.0.tar.gz ./configure --with-php-config=/opt/soft/php/bin/php-config --with-boost=/opt/soft/boost/ --with-java --prefix=/opt/soft/thrift make make install

**安装fb303**

> cd contrib/fb303 ./bootstrap.sh --prefix=/opt/soft/thrift/fb303 --with-boost=/opt/soft/boost/ --with-thriftpath=/opt/soft/thrift/ make make install

**安装scribe**

> unzip facebook-scribe-2ee14d3.zip cd facebook-scribe-2ee14d3 export BOOST_ROOT=/opt/soft/boost/ export LD_LIBRARY_PATH=/opt/soft/thrift/lib:/usr/lib:/usr/local/lib:/opt/soft/boost/lib/ ./bootstrap.sh --prefix=/opt/soft/scribe --with-boost=/opt/soft/boost/ --with-thriftpath=/opt/soft/thrift/ make make install

**启动scribe服务**

> cd facebook-scribe-2ee14d3 cp example/exmaple1.conf /opt/soft/scribe/bin cd /opt/soft/scribe/bin/ export BOOST_ROOT=/opt/soft/boost/ export LD_LIBRARY_PATH=/opt/soft/thrift/lib:/usr/lib:/usr/local/lib:/opt/soft/boost/lib/ ./scibed example1.conf

**生成客户端使用scribe**

> 先产生java的thrift代码: cd facebook-scribe-2ee14d3/if/ thrift -r -I ../../thrift-0.5.0/contrib/（这里需要指到你的thrift的源码目录） -gen java scribe.thrift

**使用**

> gen-java目录下有thrift的客户端，即取即用。javaeye里有一篇写配置log4j使用scribe的不错：http://www.javaeye.com/topic/800208

## Comments

**[long904](#15017 "2012-09-05 18:37:45"):** boost1.45 thritf0.7 安装好了，通过scribe写到本地，也就是file_path=/tmp/scribetest 没问题。但如果写入hdfs就会报“[hdfs] ERROR: HDFS is not configured for file”错误，查过资料说配置CLASSPATH，但已经配置了。还是不行。 请问这是为何呢？

**[54chen](#15019 "2012-09-06 17:13:07"):** 建议不要使用直接入hdfs的接口，写本地文件后用hive的dump很有保障。

**[54chen](#14897 "2012-05-16 21:27:32"):** 最好用hive的load file去hdfs，scribe的hdfs不是特别稳定。

**[fjpqzm](#15107 "2012-10-29 23:21:54"):** 请教下，scribe仅是用于聚合日志，那收集起来的日志一般都会采用什么工具去分析呢？我现在想把多个分布式应用的日志收集起来，进行分析，然后能序列化的查询交易的完整日志或进一步可以做些统计的。

**[54chen](#15109 "2012-10-30 11:21:22"):** 再搞一个hadoop+hive，把日志导进去，然后用hive的sql查询既是。

**[54chen](#13369 "2011-02-24 11:51:43"):** 使用scribe可以很简单写到hdfs上 :)

**[54chen](#13701 "2011-07-27 15:39:59"):** 检查一下boost的环境变量是否正常

**[wwek](#13372 "2011-02-25 18:38:32"):** facebook背后自家的开源软件还不少。

**[royce wong](#13695 "2011-07-26 11:47:33"):** 我在搞scribe 静态编译时 发现scribe 不能找到boost静态库。查看scribe生成的makefile，已经对boost的库做了指定，但还是报错： scribe_server.cpp:(.text+0xe5): undefined reference to `boost::system::system_category()' 请问有碰到过么？是不是scribe 不能静态编译？

**[Timo](#13368 "2011-02-24 11:40:14"):** 为什么不跟人人一样用Hadoop来处理日志呢？

**[54chen](#13374 "2011-02-25 20:35:05"):** 是的，thrift scribe hive都很有用。

**[addcn](#13377 "2011-03-03 00:20:34"):** scribe日志系统安装笔记 http://addcn.blogbus.com/logs/104688875.html

**[剑舞](#14896 "2012-05-16 17:37:37"):** 利用scribe写入hdfs的时候，是否有遇到过内存泄露的问题，例如我通过scribe向hdfs写入1G的数据，内存占用很容易就飙升到700M以上，数据写完之后被占用的内存仍然不被释放 系统环境 Red Hat 4.1.2-48 hadoop-0.20.2-cdh3u2

