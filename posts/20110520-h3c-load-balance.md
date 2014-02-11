title: 巧用交换机，瞬间切流量
link: http://www.54chen.com/_linux_/h3c-load-balance.html
author: 54chen
description: 
post_id: 1707
created: 2011/05/20 12:13:29
created_gmt: 2011/05/20 04:13:29
comment_status: open
post_name: h3c-load-balance
status: publish
post_type: post

# 巧用交换机，瞬间切流量

![h3c](http://img04.taobaocdn.com/imgextra/i4/T1_NWcXbNXXXcQbh6a_121410.jpg) 需求： 1号机服务移到2号机，然后要把1号机搬走。 原理： 交换机有ip对应mac地址的缓存，只需复制到ip和mac，可以在机房内的局域网里瞬间切换到另一个机器上。 操作： 1）在2号机准备好1号机的服务，并测试通过。记录1号机的mac和ip 2）以下操作要双开窗口，快速操作 3）在1号机执行：ifocnfig eth0 down 4）在2号机执行： ifconfig eth0:0 123.123.123.1 ifconfig eth0:0 hw ether 111111111111[1号机mac] ifconfig eth0:0 up 5）恭喜成功了！ 6）搬走1号机，再也不要回来！ 7）在2号机/etc/rc.local里添加： ifconfig eth0:0 123.123.123.1 ifconfig eth0:0 hw ether 111111111111[1号机mac] ifconfig eth0:0 up 终。

## Comments

**[ybjnew](#15442 "2013-03-19 17:02:41"):** 这也叫"瞬间切流量"? 最合理的方法, 应该是用负载均衡前端, 比如nginx, lvs , 以及硬件均衡服务器等. 先把对1号机的请求都转到2号机上, 然后把1号机搬走.

**[ERROR-鉴](#13570 "2011-05-24 10:58:53"):** 用别名解决问题，强！

