title: 如何配置Plproxy访问一个PostgreSQL集群
link: http://www.54chen.com/life/%e5%a6%82%e4%bd%95%e9%85%8d%e7%bd%aeplproxy%e8%ae%bf%e9%97%ae%e4%b8%80%e4%b8%aapostgresql%e9%9b%86%e7%be%a4.html
author: cc0cc
description: 
post_id: 253
created: 2008/12/10 10:39:39
created_gmt: 2008/12/10 02:39:39
comment_status: open
post_name: %e5%a6%82%e4%bd%95%e9%85%8d%e7%bd%aeplproxy%e8%ae%bf%e9%97%ae%e4%b8%80%e4%b8%aapostgresql%e9%9b%86%e7%be%a4
status: private
post_type: post

# 如何配置Plproxy访问一个PostgreSQL集群

### 假设

现在假设我们有这样一个环境： Node_proxy，上面是plproxy 的运行环境；IP: 192.168.1.253, postgresql 数据库端口 5432，数据库是testproxy Node_db1，是第一个数据库节点；IP：192.168.1.1, postgresql数据库端口 5432，上面数据库名是 test1 Node_db1，是第二个数据库节点；IP：192.168.1.2, postgresql数据库端口 5432，上面数据库名是 test2 我们假设我们下面的所有脚本都保存在plproxy节点上，并且也在plproxy节点上执行所有的命令。 

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E9%85%8D%E7%BD%AE%E4%B8%80%E4%B8%AA%E4%BD%BF%E7%94%A8plproxy%E7%9A%84PostgreSQL%E6%95%B0%E6%8D%AE%E5%BA%93%E9%9B%86%E7%BE%A4&action=edit&section=3)] 需求

我们希望Node_proxy是plproxy的节点，而Node_db1和Node_db2是底层的数据库节点。我门的目标是希望在 Node_proxy 上头执行查询，能够同时查询 Node_db1和Node_db2 上面的数据并返回结果。 那么这个工作可以利用下面的步骤来实现。 

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E9%85%8D%E7%BD%AE%E4%B8%80%E4%B8%AA%E4%BD%BF%E7%94%A8plproxy%E7%9A%84PostgreSQL%E6%95%B0%E6%8D%AE%E5%BA%93%E9%9B%86%E7%BE%A4&action=edit&section=4)] 配置前提

首先，我们要保证 Node_proxy 这个节点上的 plproxy /plpgsql语言已经正确安装，如果用我们的制作包制作，那么只要是用 createdb 创建出来的数据库，都已经内置了plproxy/plpgsql了，也就是说，你只要 /usr/local/pgsql/bin/createdb testproxy # testproxy 是测试用数据库名称 这样出来的 testdb 里面就已经有plproxy/plpgsql了，我们只要用下面的简单语句在psql中测试一下即可：   
    
    
      psql testproxy # 连接数据库
      create or replace function plproxyTest()returns integer as$$begin return 1 end;$$language plsql; #测试SQL

语句返回成功，表明plpgsql是正常的，我们首先要保证plpgsql已经安装， plproxy会在后面进行测试。 

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E9%85%8D%E7%BD%AE%E4%B8%80%E4%B8%AA%E4%BD%BF%E7%94%A8plproxy%E7%9A%84PostgreSQL%E6%95%B0%E6%8D%AE%E5%BA%93%E9%9B%86%E7%BE%A4&action=edit&section=5)] 配置过程

首先为 plproxy 创建一个 schema： 
    
    
      create schema plproxy;

    **注意:**目前这块是 plproxy 的硬编码，所以 
plproxy 的配置是通过三个函数（过程）实现的，这三个函数的标准模版如下： 
    
    
    CREATE OR REPLACE FUNCTION plproxy.get_cluster_partitions(cluster_name text)
    RETURNS SETOF text AS $$
    BEGIN
        IF cluster_name = 'MyCluster' THEN
            RETURN NEXT 'dbname=test1 host=192.168.1.1';
            RETURN NEXT 'dbname=test2 host=192.168.1.2';
            RETURN;
        END IF;
        RAISE EXCEPTION 'Unknown cluster';
    END;
    $$ LANGUAGE plpgsql;

上面这个函数是让plproxy可以找到对应的集群，那么这里的 MyCluster 是集群的名称，根据自己的需要制定，这个名称在后面的查询的时候就有用了；这里的dbname, host 等参数，就是PostgreSQLB标准的数据库连接串的配置方法，注意我们这里和上面的Node_db1以及Node_db2对应； 第二个函数是： 
    
    
    CREATE OR REPLACE FUNCTION plproxy.get_cluster_version(cluster_name text)
    RETURNS int4 AS $$
    BEGIN
        IF cluster_name = 'MyCluster' THEN
            RETURN 1;
        END IF;
        RAISE EXCEPTION 'Unknown cluster';
    END;
    $$ LANGUAGE plpgsql;

这个函数其实是plproxy用于判断是否给前端返回已经cache过的结果用的，这样，因为函数本身可以动态更新（无需down机），那么我们可以通过重新创建函数，返回不同RETURN的值，实现cache的失效控制。 第三个函数是： 
    
    
    create or replace function plproxy.get_cluster_config(cluster_name text, out key text, out val text)
    returns setof record as $$
    begin
        key := 'statement_timeout';
        val := 60;
        return next;
        return;
    end; $$ language plpgsql;

这个函数其实是获取不同的集群的配置，我们这里可以给不同的集群（比如MyCluster等）不同的类似超时时间、长短连接等的设置，具体参数我们会在[用户手册](http://10.62.164.49/wiki/index.php/%E7%94%A8%E6%88%B7%E6%89%8B%E5%86%8C)里详细给出，我们上面这个例子缺省认为所有的集群都用一个配置，因为这个函数也可以运行时动态create or replace，所以问题不大。 书写了这三个函数之后，我们就可以在plproxy的节点上，也就是Node_proxy上，用psql在指定的数据库（我们的例子是 testdb）上运行这些SQL命令。这里我假设我们把上面的SQL脚本都保存在一个叫 MyClusterInit.sql 的文件里， 那么我们可以这么执行： 
    
    
      psql -f MyClusterInit.sql -d testproxy

这样，plproxy的设置就完成了。然后，我们继续一个很重要的工作：我们希望在plproxy节点上执行 SQL查询，获取两个数据库节点（Node_db1和Node_db2）的数据（因为我们的数据很可能已经分散在那两个数据库上头了，所以需要获取两个数据库对的同样查询的返回）。 

### [[编辑](http://10.62.164.49/wiki/index.php?title=%E9%85%8D%E7%BD%AE%E4%B8%80%E4%B8%AA%E4%BD%BF%E7%94%A8plproxy%E7%9A%84PostgreSQL%E6%95%B0%E6%8D%AE%E5%BA%93%E9%9B%86%E7%BE%A4&action=edit&section=6)] 数据库节点的设置

OK，我们给每个数据库节点都创建一个函数，这个函数是这样的： 
    
    
    create or replace function public.dquery(query text) returns setof record as $$
    declare
       ret record;
    begin
         for ret in execute query loop
         return next ret;
         end loop;
         return;
    end;
    $$ language plpgsql;
    
    create or replace function public.ddlExec(query text) returns integer as $$
    declare
       ret integer;
    begin
    	execute query;
       return 1;
    end;
    $$ language plpgsql;
    
    create or replace function public.dmlExec(query text) returns integer as $$
    declare
       ret integer;
    begin
    	execute query;
       return 1;
    end;
    $$ language plpgsql;

这几个函数实际上是非常通用的函数，它可以接收一个SQL语句，然后执行之、返回这个SQL语句的结果。区别只是一个是数据定义操作，一个是数据更新操作，一个是假设，我们把这个函数保存在 MyClusterNodesInit.sql，那么我们可以这样创建它： 
    
    
      psql -f MyClusterNodesInit.sql -h 192.168.1.1 -d test1
      psql -f MyClusterNodesInit.sql -h 192.168.1.2 -d test2

    **注意：**这个函数是建立在节点数据库上的，所以我直接用 -h 参数做了配置。 

    **注意：**节点数据库必须允许proxy数据库访问，也就是说Node_db1和Node_db2上的postgresql 
必须支持 Node_proxy 对本机数据库的访问，这个可以通过在 Node_db1和Node_db2的postgresql数据库的pg_hba.conf文件里添加下面一行来实现（之后要 pg_ctl reload -D /path/to/dbdir重载配置）： 
    
    
      host    all         all         192.168.1.253/32          trust

执行完上面的东西之后，就可以在 proxy 节点上创建一个同名的函数，用于进行集群检索，这个函数是这样的： 
    
    
    CREATE OR REPLACE FUNCTION public.dquery(query text) RETURNS setof
    record AS $$
    CLUSTER 'MyCluster';
    RUN ON ALL without transaction;