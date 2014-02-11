title: “先进的互联互通技术”–使用ice联通php和java [part 2 client]
link: http://www.54chen.com/php-tech/the-advanced-interconnection-technology-the-use-of-ice-unicom-php-and-java-part-2-client.html
author: cc0cc
description: 
post_id: 784
created: 2009/09/27 10:51:01
created_gmt: 2009/09/27 02:51:01
comment_status: open
post_name: the-advanced-interconnection-technology-the-use-of-ice-unicom-php-and-java-part-2-client
status: publish
post_type: post

# “先进的互联互通技术”–使用ice联通php和java [part 2 client]

[信曾哥，转载的都给我保留原链接地址：<http://www.54chen.com/784-the-advanced-interconnection-technology-the-use-of-ice-unicom-php-and-java-part-2-client/>] 这一part的目标是建立起ice的php环境，以及写PHP代码调用上一part中的10000端口的java代码。 在part 1中，我们建立了一个Server在10000端口等着我们，地址见 <http://www.54chen.com/779-the-advanced-interconnection-technology-the-use-of-ice-unicom-php-and-java/> **建立带ice的PHP环境** 不管是nginx+php-cgi还是别的啥，这里只表述php编译过程。 

> **本例以 Ice 3.2.1为例 下载的位置和参考的文档都在下面** <http://www.zeroc.com/download_3_2_1.html> <http://www.zeroc.com/doc/Ice-3.2.1/manual/Slice.5.10.html#50592>

1.下载IcePHP包，进行编译。在此之前，你还需要安装Ice，记好使用3.2.1版本，如果你是用的tar.gz包，在/opt/Ice-3.2.1/，如果是rpm包，在/usr/下。 2.运行 export ICE_HOME=/opt/Ice-3.2.1 设置好ICE_HOME后进IcePHP目录直接make  make install。 此时在/opt/IcePHP-3.2.1/lib64下有产生IcePHP.so（也就是传说中的PHP扩展的so文件了） 3.vi /etc/php.ini 在任意位置增加两行： 

> extension=IcePHP.so ice.profiles = /etc/ice.prop

4.准备/etc/ice.prop 

> vi /etc/ice.prop

加入以下两行： 

> [profile_demo] ice.slice=/root/zhen.chen/example/demo.ice

这两行的意思就是，增加一个ice的配置，名字是profile_demo，slice文件是...demo.ice 

> PHP不能在一个php进程中同时调用多个的ice slice，只能通过在prop文件中写义不同的段，然后在php文件中使用类似Ice_loadProfile('profile_demo')来告诉ice这次访问是使用的哪一个slice文件，当然，不能在一个文件里同时有两个Ice_loadProfile的，如果有两个接口的调用需求，只能是通过前端的js来发起两次请求才行了。

5.把上一节提到的demo.ice文件放到对应的[目录](/779-the-advanced-interconnection-technology-the-use-of-ice-unicom-php-and-java/)下 到此，带有ICE的PHP环境建立完成。 **写PHP的客户端来调用 java里的Demo.test.excute方法**

> vi Demo.php <?php ini_set("display_errors", "1"); global $ICE; Ice_loadProfile('profile_demo'); try { $vBase = $ICE->stringToProxy ( "TestAdapter:default -p 10000" ); $vManager = $vBase->ice_checkedCast ( "::Demo::test" ); $vProperties = $vManager->execute("Hello "," world!"); echo $vProperties; } catch ( Exception $vError ) { print_r($vError); } ?>

注意全角半角。 通过浏览器访问[Demo](/469-dyang%e5%90%8c%e5%bf%97%e7%9a%84%e6%97%a0%e5%90%8d%e6%b0%8f%e7%9a%84%e4%bb%a3%e7%a0%81%e5%ad%a6%e4%b9%a0%e8%ae%b0%e5%bd%95-flex-2d-rpg%e6%b8%b8%e6%88%8fdemo/).php 你是不是看到了久违的Hello World!了呢？ 截图： ![](http://www.54chen.com/wp-content/uploads/2009/09/qqe688aae59bbee69caae591bde5908d.jpg) ice、php和java的关系架构图： ![](http://www.54chen.com/wp-content/uploads/2009/09/qqe688aae59bbee69caae591bde5908d1.jpg)

## Comments

**[kangzj](#14785 "2012-03-31 13:44:51"):** 把slice编译成php之后，就可以调用“两个slice”了，不过只可以有一个communicator

**[tim](#11899 "2009-09-27 21:41:05"):** 我个人不太看好ice这种古老的专有技术，很多年前就有了，感觉社区比较沉闷。 如果只是跨语言数据通讯，可以考虑google的protobuf 如果需要跨语言RPC, 可以考虑facebook的thrift 这2个目前都发展很快且社区非常活跃。

**[cc0cc](#11900 "2009-09-28 15:55:47"):** 呵呵 ice是挺古老的，专有是指哪方面？ protobuf和thrift都不错，都是一样的篮子，制作人不一样，所以应该都大同小异。 校内大量使用ice

