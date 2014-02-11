title: PostgreSQL系统安装(Linux) 
link: http://www.54chen.com/life/postgresql%e7%b3%bb%e7%bb%9f%e5%ae%89%e8%a3%85linux.html
author: cc0cc
description: 
post_id: 105
created: 2008/11/21 14:51:32
created_gmt: 2008/11/21 06:51:32
comment_status: open
post_name: postgresql%e7%b3%bb%e7%bb%9f%e5%ae%89%e8%a3%85linux
status: publish
post_type: post

# PostgreSQL系统安装(Linux) 

* 1\. 到 [http://www.postgresql.org](http://www.postgresql.org/)下载pg8.2.4 source
* 2\. 编译安装[pgsql](/c/101)

  * tar xvfj postgresql-8.2.4.tar.bz2
  * cd postgresql-8.2.4
  * ./configure --prefix=/home/y/pgsql #注意下一步把这个目录加入你bash的PATH中去
  * gmake
  * sudo gmake install
  * sudo mkdir /home/y/pgsql/data
  * sudo chown yahoo /home/y/pgsql/data
  * sudo -u yahoo /home/y/pgsql/bin/initdb --locale=zh_CN.UTF-8 --encoding=utf8 -D /home/y/pgsql/data
* 3.创建数据库 

  * cd /home/y/pgsql/bin
  * sudo -u yahoo ./pg_ctl start -D /home/y/pgsql/data #启动服务进程
  * sudo -u yahoo /home/y/pgsql/bin/createdb test　#创建数据库test
  * sudo -u yahoo /home/y/pgsql/bin/psql test
  * test=# create role changxin superuser login ;
  * test=# \q
  * 备注：现在就可以直接登入test数据库进行操作了。并且我的权限是最大权限的superuser。