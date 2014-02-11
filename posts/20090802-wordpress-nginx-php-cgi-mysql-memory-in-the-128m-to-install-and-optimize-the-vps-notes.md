title: wordpress nginx php-cgi mysql在128m内存下的vps安装和优化手记[原创]
link: http://www.54chen.com/architecture/wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes.html
author: cc0cc
description: 
post_id: 688
created: 2009/08/02 15:34:49
created_gmt: 2009/08/02 07:34:49
comment_status: open
post_name: wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes
status: publish
post_type: post

# wordpress nginx php-cgi mysql在128m内存下的vps安装和优化手记[原创]

[ 文章作者：陈臻 本文版本：v1.0 最后修改：2009.8.2 转载请注明原文链接：<http://www.54chen.com/688-wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes/> ] 选取CentOS，因为它是号称最安全及性能都相对较好的Linux系统。系统内存128m，系统用掉30m，有100m左右可用（如图1所示），swap已经有256M，硬盘为5G，除去系统后大约有4G可供捣腾。 ![](/wp-content/uploads/2009/08/1.jpg) 图1。   首先，在空白的系统上使用下面的命令，安装gcc等一堆工具和后面php会用到的一些包： `yum -y install gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers` yum这一堆东西会有点慢，这里正好插播一下，mysql的编译在128的内存下非常慢，所以呆会儿我们会采取直接yum，php、nginx都下源码编译，经过以往的经验eAccelerator、Xcache和Zend Optimizer这三者，最好是用eAccelerator搭配Zend Optimizer能给php加速得到最佳效果，当然了，Zend Optimizer需要Zend Guard来搭配，而后者是收费的，伟大的中国人有伟大的破解。 我执行的时候耗时32分钟左右。 言归正传，继续： `wget http://sysoev.ru/nginx/nginx-0.7.61.tar.gz wget http://www.php.net/get/php-5.2.10.tar.gz/from/this/mirror wget http://blog.s135.com/soft/linux/nginx_php/phpfpm/php-5.2.10-fpm-0.5.11.diff.gz wget http://bart.eaccelerator.net/source/0.9.5.3/eaccelerator-0.9.5.3.tar.bz2 wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-7.9.tar.gz [nginx rewrite使用]` 【安装[MySQL](http://www.54chen.com/19-mysql%e6%93%8d%e4%bd%9c%e8%a6%81%e7%82%b9%e6%95%b4%e7%90%86/)】 `yum install -y mysql-server 启动MySQL: service mysqld start 设置mysql数据库root帐号密码: mysqladmin -u root password 'newpassword' [引号内填密码] 让mysql更安全： mysql -u root -p [此时会要求你输入刚刚设置的密码，输入后回车即可]` mysql> DROP DATABASE test; [删除test数据库] mysql> DELETE FROM mysql.user WHERE user = ''; [删除匿名帐户] mysql>DELETE FROM mysql.user WHERE password = '';[删除无密码帐户] mysql> FLUSH PRIVILEGES; [重载权限] 【优化MySQL】 此时的mysql直接吃掉20m左右的内存，如图2所示。我们要对其进行优化，关掉[innodb](http://www.54chen.com/358-mysql%e7%9a%84%e6%80%a7%e8%83%bd%e8%b0%83%e4%bc%98%e5%b7%a5%e5%85%b7%ef%bc%9a%e6%af%94mysqlreport%e6%9b%b4%e6%96%b9%e4%be%bf%e7%9a%84tuning-primersh/)。 ![](http://www.54chen.com/wp-content/uploads/2009/08/2.jpg)图2。         vi /etc/my.cnf 在后面增加如下内容： `skip-innodb [mysql] no-auto-rehash` [mysqld] user = mysql port = 3306 open_files_limit = 600 back_log = 20 max_connections = 100 max_connect_errors = 200 table_cache = 60 external-locking = FALSE max_allowed_packet = 16M sort_buffer_size = 128K join_buffer_size = 128K thread_cache_size = 10 thread_concurrency = 8 query_cache_size = 0M query_cache_limit = 2M query_cache_min_res_unit = 2k default_table_type = MyISAM thread_stack = 192K transaction_isolation = READ-UNCOMMITTED tmp_table_size = 512K max_heap_table_size = 32M long_query_time = 1 log_long_format server-id = 1 binlog_cache_size = 2M max_binlog_cache_size = 4M max_binlog_size = 512M expire_logs_days = 7 key_buffer_size = 4M read_buffer_size = 1M read_rnd_buffer_size = 2M bulk_insert_buffer_size = 2M myisam_sort_buffer_size = 4M myisam_max_sort_file_size = 10G myisam_max_extra_sort_file_size = 10G myisam_repair_threads = 1 myisam_recover [mysqldump] quick max_allowed_packet = 16M 【重启MySQL】 service mysqld restart 再看mysql吃掉的内存，已经降低了四分之一，如图3所示： ![](http://www.54chen.com/wp-content/uploads/2009/08/3.jpg)图3。         【安装php】 首先使用yum安装上mysql的开发包，供[php](http://www.54chen.com/490-%e5%a6%82%e4%bd%95%e7%94%a8php%e4%bb%a3%e7%a0%81%e6%b8%85%e7%a9%basquid%e7%bc%93%e5%ad%98%e4%b8%8b%e7%9a%84%e6%8c%87%e5%ae%9a%e6%96%87%e4%bb%b6/)扩展mysql使用： `yum -y install mysql-devel 安装patch工具： yum -y install patch tar zxvf php-5.2.10.tar.gz gzip -cd php-5.2.10-fpm-0.5.11.diff.gz | patch -d php-5.2.10 -p1 cd php-5.2.10 ./configure --prefix=/opt/php --with-config-file-path=/opt/php/etc --with-mysql=/usr/share/mysql --with-mysqli=/usr/bin/mysql_config --with-iconv-dir=/usr/local --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-discard-path --enable-safe-mode --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --with-curlwrappers --enable-mbregex --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --enable-mbstring --with-gd --enable-gd-native-ttf --with-openssl --enable-pcntl --enable-sockets --with-ldap --with-ldap-sasl --without-pear make && make install` 把pear手动装上(这个是5.2.10的一个bug，后面可能已经打补丁了)： curl http://pear.php.net/go-pear | /opt/php/bin/php cp php.ini-dist /opt/php/etc/php.ini 【安装eaccelerator】 `tar jxvf eaccelerator-0.9.5.3.tar.bz2 cd eaccelerator-0.9.5.3 /opt/php/bin/phpize ./configure --enable-eaccelerator=shared --with-php-config=/opt/php/bin/php-config make && make install` mkdir -p /opt/eaccelerator_cache vi /opt/php/etc/php.ini 添加下面的内容： `[eaccelerator] zend_extension="/usr/local/webserver/php/lib/php/extensions/no-debug-non-zts-20060613/eaccelerator.so" eaccelerator.shm_size="64" eaccelerator.cache_dir="/usr/local/webserver/eaccelerator_cache" eaccelerator.enable="1" eaccelerator.optimizer="1" eaccelerator.check_mtime="1" eaccelerator.debug="0" eaccelerator.filter="" eaccelerator.shm_max="0" eaccelerator.shm_ttl="3600" eaccelerator.shm_prune_period="3600" eaccelerator.shm_only="0" eaccelerator.compress="1" eaccelerator.compress_level="9"`   【安装[Zend](http://www.54chen.com/157-php%e4%bb%a3%e7%a0%81%e7%9a%84%e4%bc%98%e4%b8%8e%e5%8a%a3/) Optimizer】 要去zend.com上注册用户名后进download页面下载，这里太弱智了，不知道zend是否有专门的产品人员，这种注册下载有什么意义，只是一堆死账号而已。 下载ZendOptimizer-3.3.3-linux-glibc23-i386.tar.gz `tar -zxvf ZendOptimizer-3.3.3-linux-glibc23-i386.tar.gz cd ZendOptimizer-3.3.3-linux-glibc23-i386 ./install` 运行过程中会要求你写些安装路径啥的，照着写就是。 如果出现类似下面的错误： ./install-tty: line 139: ./php: cannot execute binary file 那说明你下错了包了，这种情况是因为你32位的系统下了64位的包或者是反过来。 【安装nginx】 `tar -zxvf pcre-7.9.tar.gz cd pcre-7.9 ./configure make && make install cd ..` tar zxvf nginx-0.7.61.tar.gz cd nginx-0.7.61 ./configure --user=www --group=www --prefix=/opt/nginx --with-http_stub_status_module --with-http_ssl_module make && make install 增加www用户： groupadd www useradd -g www www 【优化php\nginx】 1.优化php-fpm.conf `vi /opt/php/etc/php-fpm.conf

## Comments

**[江江](#10688 "2009-08-02 15:40:38"):** 测试顶一个

**[金山老罗](#10714 "2009-08-03 16:22:31"):** 友情顶贴

**[zhu](#10768 "2009-08-05 21:22:54"):** 发现有验证码了，是不是树大招风了 ^-^

**[cc0cc](#10771 "2009-08-05 21:51:52"):** 一直就有国外的垃圾机器人来发广告呢，这次优化把原来看不倒的基于cookies的antispam插件换成现在这个了，原来的太慢了。冒似这个验证码非常难认

**[cc0cc](#10772 "2009-08-05 21:54:21"):** 老罗驾到 哈哈

**[美国VPS推荐](#10987 "2009-08-11 10:45:01"):** 我好几个WP的blog整天遭受国外垃圾评论骚扰，不错都被屏蔽，嘿嘿

**[cc0cc](#10990 "2009-08-11 15:55:22"):** 兄弟你再用这个名字我就要删除了哈

**[alswl](#13944 "2011-11-01 08:47:14"):** 大哥，您现在还是用的128M VPS么，我并发10个连接，击中1749次，100%命中，太牛逼了

**[54chen](#13947 "2011-11-01 19:38:01"):** 是的

**[alswl](#13943 "2011-10-31 21:19:15"):** 感谢您的教程，终于把vps内存降下来了

**[王猛](#11627 "2009-08-18 03:04:46"):** hi,朋友。 我们是不是邻居呢？ 我和你是同一间公司的VPS，我在硅谷机房。 我是一台256M的VPS，我用的是Ubuntu 9.04这个系统。 我的整个配置都是自己编译而来的，使用了intel c++这个编译器。 从CentOS 5.1全部yum来的apache mysql php，只能承受162个攻击。 而在我自行编译配置后，已经可以达到600次攻击的水准。 我想说的是，这个nginx的配置里有一个问题。目前，客户基本上都是XP系统，而这个系统是IE6作为默认浏览器。IE6支持HTTP 1.1标准。 配置上，写的1.0. nginx的处理过程是当客户以1.1协议访问时，进行缓存。当客户以1.0访问时，丢弃原来的1.1缓存，重新生成；当1.1客户来访时，1.0缓存又一次丢失，再重新生成。 -_- 考虑1.1用的人多余1.0，因此最好改为1.1。

**[王猛](#11630 "2009-08-18 03:07:08"):** -_- 当我给你post完这篇日志的时候，0.8.9发布了。 我回去升级我的nginx了。

**[hnws](#11859 "2009-09-02 22:22:31"):** 请问你开了多少个 php_cgi 的进程 ？

**[cc0cc](#11860 "2009-09-03 18:27:35"):** 5个 效果还行

**[惑者](#11862 "2009-09-04 01:20:09"):** 我也在用 rashost 的128VPS，不过用的Ubuntu9.04 同样也安装了Nginx Wordpress，其中Nginx是编译的，其他都是aptitude的 不过mysql 一起来内存就被吃光了，虚存都被吃了一半 访问页面比蜗牛还慢，不知道是配置问题还是安装的包不对 看到这个帖子，要仔细学习下，希望站长多指教

**[cc0cc](#11864 "2009-09-04 12:04:32"):** mysql的innodb引擎比较吃内存 可以关掉 方法见正文 默认的php-cgi模块比较多 也比较吃内存 建议自己编译

**[惑者](#11865 "2009-09-04 14:38:46"):** 我遇到个比较奇怪的现象，通过aptitude 安装mysql-server-5.0之后 启动正常，然后我开始按照本博文的介绍修改配置文件 restart显示失败，然后我将原来的配置文件恢复，依旧显示失败 请问看哪个log能够帮助判断出问题所在？

**[cc0cc](#11866 "2009-09-04 16:14:14"):** 你可以看看 /var/log/mysqld.log 不确定这个路径是否一定在

**[unixhater](#11875 "2009-09-19 17:46:23"):** 你好，我根据你的配置一步步来安装，发现一些错误 mysql删用户的命令有中文 安装ea时，php.ini设置中的引号带中文，还有目录写错了（貌似这配置与张宴那篇神似^_^） nginx的configure时有中文会出错 另外我在pear,ZendOptimizer安装，与nginx,php-fpm用户设置时碰到点小问题，不过都解决了。

**[cc0cc](#11876 "2009-09-19 23:35:07"):** 呵呵 兄弟真是细心 这个blog系统比较原始 以至于半角的东西全被自动转成了全角 不知道有什么好的插件可以解决这个问题不

**[比卡超](#11973 "2009-11-04 20:39:25"):** 请问CentOS是什么版本呢？？？

**[cc0cc](#11976 "2009-11-05 09:29:59"):** redhat.com发布redhat 9(简写为rh9)后，不再开发redhat 10,11...,全面转向redhat enterprise linux(简写为rhel)的开发，和以往不同的是,新的rhel 3要求用户先购买lisence,redhat.com承诺保证产品的稳定性，安全性。rhel 3二进制代码不再提供下载，而是作为redhat 服务的一部分，但源代码依然是open。所以有了centos ,whitebox,dao 等等一批open source的企业版本，其中centos最为活跃。 \--你可以多用用搜索引擎的

**[比卡超](#11982 "2009-11-07 01:57:30"):** 可能你误会了。。我找到的Cent有1.0~5.x版本。请问你是安装哪个？

**[cc0cc](#11983 "2009-11-07 21:57:44"):** 哈哈，果真是误会了 我的是4.x版本的

**[hnws](#11994 "2009-11-14 14:43:33"):** 我的256M的VPS上系统是Fedora 11，php和php-cgi和spwan-php是使用源里的包。 已经将php-cgi的数量改为3个。 但是一直两个左右php-cgi每个占用大概50M的内存。 请问这大概是怎么回事？我是否需要提供进一步信息？ 我在google上找了许久都没有类似的情况。

**[cc0cc](#11995 "2009-11-14 20:26:23"):** php-cgi应该不是很吃内存吧，你尝试通过源码编译，尽可能减少所支持的扩展可以得到改善

**[shiny](#12007 "2009-11-27 18:44:46"):** 我用的也是rashost的128M的VPS～ 在swap使用较多的时候速度明显变慢~~ php-cgi进程大概占30MB左右，不得不改少进程数。 3个还能稳定运行~ 考虑升级内存ing

**[ss](#12100 "2010-01-06 15:18:32"):** ss

