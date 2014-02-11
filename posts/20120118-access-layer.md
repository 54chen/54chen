title: 服务接入层小结
link: http://www.54chen.com/architecture/access-layer.html
author: 54chen
description: 
post_id: 1980
created: 2012/01/18 13:10:31
created_gmt: 2012/01/18 05:10:31
comment_status: open
post_name: access-layer
status: publish
post_type: post

# 服务接入层小结

![access,layer](http://img04.taobaocdn.com/imgextra/i4/13078490/T2evmjXahaXXXXXXXX_!!13078490.jpg) **接入层** 是指普通用户与核心业务逻辑中间的过程。现在常见的服务接入层，像nginx、lvs、php这些都可以算是。 **观点** 一种观点认为：接入的量不应该影响系统的正常运行，也就是不能接入不能处理的用户请求，类似上图中前图。 另一种观点认为：接入层应该尽量接入所有用户，类似上图中后图。 **做法** nginx： nginx如果用做proxy，后端挂其他服务的话，是一个纯粹的NIO server。对用户来说，nginx是长连接（http 1.1），对后端服务来说，nginx是短连接（http 1.0）。 

> default: proxy_connect_timeout 60s; 向后端服务创建连接的超时时间 default: proxy_read_timeout 60s; 后端返回请求响应超时时间 default: proxy_send_timeout 60s; 大请求发到后端超时时间

nginx通过上面的三个设置，当后端有响应不过来的时候，进行超时处理（记录日志），当发现超时很多了，就需要提升后端的处理能力了。 那么，nginx自身的处理能力是否有上限呢？当然有! 

> worker_processes 1; worker_connections 1024; max_clients = worker_processes * worker_connections 

当超过max_clients的用户扑上来的时候，如果幸运后端没有能力问题，那就会看到nginx在日志中要求你增加worker的提示。 php-fpm： 

> pm = dynamic/static; dynamic时有最大进程最小进程之说，static时没有。只有一个上限。 pm.max_children = 50;强制的一个阀门，后面的设置里，进程数不能大于这个。 pm.start_servers = 20;启动时启动的fastcgi进程。 pm.min_spare_servers = 5;最小fastcgi进程数量，dynamic有效 pm.max_spare_servers = 35;最大fastcgi进程数量，dynamic有效

php没有异步进程概念，如果要使用php作为接入层，需要自己完成NIO扩大接入能力（接触到的ICE、thrift在php中都是同步操作）。而接入能力就取决于上面的配置。 java: java优秀的NIO框架netty。 servlet3.0实现了异步请求的context，目前还没有在平常框架中进行使用。一般的用法为comet push server，性能未知。 一般来说，用java做接入层很容易变成了业务层。（但是，为什么不呢？） 

> tomcat 7.0.x提供了servlet3.0的这一特性，CometProcessor interface。 resin 4.x提供了servlet3.0的这一特性，GenericCometServlet。

从名字看，大家都一致性地认为这一特性适合用来做comet server。（理论上可接入无限用户，直到费尽cpu、内存，服务器死机、jvm oom）

## Comments

**[nodexy](#14272 "2012-01-18 13:37:46"):** 看起来好适合铁道部啊 哈哈 个人经历的大多都是第一种，只有极少数情况下子系统划分得非常“开”的时候才使用了第二种。

