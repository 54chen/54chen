title: hive迁移hadoop namenode时遇到的问题
link: http://www.54chen.com/architecture/hive-hadoop-namenode-problem.html
author: 54chen
description: 
post_id: 1739
created: 2011/06/14 09:35:35
created_gmt: 2011/06/14 01:35:35
comment_status: open
post_name: hive-hadoop-namenode-problem
status: publish
post_type: post

# hive迁移hadoop namenode时遇到的问题

![hadoop hive namenode 迁移](http://img04.taobaocdn.com/imgextra/i4/T1_OqfXfNnXXbx.crb_093330.jpg) 使用了一段时间的hive和hadoop，在迁移datanode时很容易，要迁移namenode，hive里就闹情绪了。更改namenode后，在hive中运行select 依旧指向之前的namenode地址。 要如何解决呢，具体操作如下： 1.复制打包老的namenode 2.复制打包老的namenode的hdfs目录 3.到新机器准备好：修改master的值 复制到各节点 修改hive的定义 4.hive这里使用了mysql来保存metadata 因为这些信息会被写死在hive的DBS和SBS表里，下面是关键的一步： 进入mysql，执行下面的sql， 

> update DBS set DB_LOCATION_URI=REPLACE(DB_LOCATION_URI,'old host','new host'); update SDS set LOCATION=REPLACE(LOCATION,'old host','new host');

启动，一切正常了！迁移hive namenode成功。

## Comments

**[54chen](#13597 "2011-06-15 21:35:58"):** 对不起，我链接的都是认识的朋友们

**[SEVEN](#13596 "2011-06-15 17:30:31"):** 我从google无意中发现了这里,在这边我看了你写的很多文章,发现你在技术方便见解很独到,我非常喜欢阅读这边的文章,不知道能否和贵站交换一下友情链接呢 贵站的链接我已经做好了(可以打开http://hi.baidu.com/see7di/home查看),期待回应 以下是我的链接信息: Seven的Blog http://hi.baidu.com/see7di/home

**[lianhuiwang](#13592 "2011-06-14 09:52:12"):** 用hostname就不会这么麻烦了。

