title: 使用PostgreSQL的dbi link包
link: http://www.54chen.com/life/%e4%bd%bf%e7%94%a8postgresql%e7%9a%84dbi-link%e5%8c%85.html
author: cc0cc
description: 
post_id: 266
created: 2008/12/10 10:54:02
created_gmt: 2008/12/10 02:54:02
comment_status: open
post_name: %e4%bd%bf%e7%94%a8postgresql%e7%9a%84dbi-link%e5%8c%85
status: private
post_type: post

# 使用PostgreSQL的dbi link包

## 目的

因为工作需要，不得不面对纷繁复杂的众多的不同类型的数据库，为了降低难度，尤其是数据转存的复杂性，我们可以充分利用伟大的CPAN和同样伟大的 PostgreSQL 提供的接口，在PG里面，用PG的语法访问不同的数据库。这个工具就是PG的一个组件包 dbi-link。 

## [[编辑](http://10.62.164.49/wiki/index.php?title=%E4%BD%BF%E7%94%A8PostgreSQL%E7%9A%84dbi_link%E5%8C%85&action=edit&section=2)] dbi-link

PG的dbi-link实现了SQL2003的夸数据库访问异构数据库的一个子集，其基本原理是用Perl的DBI和相应数据库的DBD来访问异构数据库，然后结合了PG已有的SQL对象，利用PG来维护这些异构的数据库。 

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E4%BD%BF%E7%94%A8PostgreSQL%E7%9A%84dbi_link%E5%8C%85&action=edit&section=3)] 要求

  1. postgresql 编译的时候需要支持Perl，也就是要给 postgresql 的 configure 添加 --with-perl 的选项。
  2. 要有Perl的YAML CPAN包
  3. 要有Perl的DBI CPAN包
  4. 要有Perl的对应你需要访问的数据库的DBD包，比如DBD::Mysql

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E4%BD%BF%E7%94%A8PostgreSQL%E7%9A%84dbi_link%E5%8C%85&action=edit&section=4)] 下载

dbi-link是一个pgfoundry项目，可以在下面的地址下载到： 
    
    
      <http://pgfoundry.org/projects/dbi-link/>

下载的页面： 
    
    
      [http://pgfoundry.org/frs/?group_id=1000045&release_id=757](http://pgfoundry.org/frs/?group_id=1000045&release_id=757)

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E4%BD%BF%E7%94%A8PostgreSQL%E7%9A%84dbi_link%E5%8C%85&action=edit&section=5)] 安装

dbi-link本身的安装很简单，但是需要一些额外的包，主要是Perl的一些包，我这里列一些大多数系统都没有的，比如YAML，我们需要这样安装： 
    
    
      sudo su -  #需要root给全局安装
      perl -MCPAN -e 'shell' # 如果你还没配置过CPAN，请先运行这个命令配置
      perl -MCPAN -e 'install YAML'

然后就OK了。 然后，可以根据需要安装对应的DBD，比如Mysql的： 
    
    
      perl -MCPAN -e 'install DBD::Mysql'

安装完这些东西之后，我们安装 dib-link，(需要PG的超级用户权限)步骤是： 
    
    
      # cd　dbi-link-2.0.0
      # createdb dbi_link_test  #创建一个测试数据库
      # createlang plperlu -d dbi_link_test #给这个数据库创建perlu语言，dbi_link需要这个
      # psql -f dbi_link.sql -d dbi_link_test

这样dbi-link就装好了。   

## [[编辑](http://10.62.164.49/wiki/index.php?title=%E4%BD%BF%E7%94%A8PostgreSQL%E7%9A%84dbi_link%E5%8C%85&action=edit&section=6)] 使用

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E4%BD%BF%E7%94%A8PostgreSQL%E7%9A%84dbi_link%E5%8C%85&action=edit&section=7)] 初始化对应数据库

使用之前需要初始化对应的数据库，在这里我用mysql举例。 

  * 更新系统表
    
    
       UPDATE
          pg_catalog.pg_settings
       SET
          setting =
              CASE WHEN 'dbi_link' = ANY(string_to_array(setting, ','))
              THEN setting
              ELSE 'dbi_link,' || setting
              END
      WHERE
           name = 'search_path';

  * 设置连接参数，对于Mysql来说，是这样：
    
    
      SELECT make_accessor_functions('dbi:mysql:database=test;host=localhost', 'root', _,_
         '---
          AutoCommit: 1
          RaiseError: 1
         ',NULL,NULL,NULL,'mysql_test');

    

    要注意的有两个： 

  1. 第四个字段是YAML的域，最好有回车（我还没试验去掉回车是否OK）
  2. 最后的'mysql_test'这个，是准备在PG里面通过模式的方法访问mysql数据库的模式，可以根据自己需要命名，建议是mysql数据库的名称
  3. 第一个参数含义应该是一目了然的
  4. 第二个参数是连接到mysql数据库的用户名
  5. 第三个参数是连接到mysql数据库的密码，没有的话为

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E4%BD%BF%E7%94%A8PostgreSQL%E7%9A%84dbi_link%E5%8C%85&action=edit&section=8)] 查询！！！

OK，现在就可以发出查询啦！！！先确信我们的mysql数据库是启动并且正常运行。然后，假设我的mysql的test数据库里头有这个表： 
    
    
      wp_categories

那么现在我可以用这个办法在PG里直接查询它： 
    
    
      select * from mysql_test.wp_categories;

试试看？是不是很爽？发散一下，我们就可以用这种方法倒数据： 
    
    
      create table categories as select * from mysql_test.wp_categories;

试试看，是不是很爽？！ 

取自"<http://10.62.164.49/wiki/index.php/%E4%BD%BF%E7%94%A8PostgreSQL%E7%9A%84dbi_link%E5%8C%85>"