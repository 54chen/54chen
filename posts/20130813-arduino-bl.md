title: arduino-蓝牙各种版本类型及费用对比
link: http://www.54chen.com/_linux_/arduino-bl.html
author: 54chen
description: 
post_id: 2394
created: 2013/08/13 17:17:11
created_gmt: 2013/08/13 09:17:11
comment_status: open
post_name: arduino-bl
status: publish
post_type: post

# arduino-蓝牙各种版本类型及费用对比

![arduino](http://leiphone.qiniudn.com/uploads/09-2/-3/09-25-32-27.jpg) **厂商** 做单芯片系统和蓝牙模块的厂商主要有：Bluegiga（蓝兆），Texas Instruments（德州仪器），Nordic Semiconductor（北欧半导体），Cambridge Silicon Radio（CSR公司），Broadcom Corporation（博通公司），这几家公司，基本掌控了全球所有的蓝牙、无线、voip、音视频解码等核心技术。（技术门槛岗岗的！） **常见类型** _2.1+EDR_ 2004年推出，2006年大量使用。 http://blog.sina.com.cn/s/blog_8a49cc8f0101b03j.html *此款为目前最好用的一款了。 _3.0_ 2009年推出。升级的秘密在于，在需要的时候，其会调用wifi协议，传输速度提升8倍。 _4.0（BLE）_ 2010年推出。低功耗、提升传输距离为60米。android4.3才宣布完整支持。 http://www.cnblogs.com/zdz8207/archive/2012/10/17/bluetooth_ble_android.html http://redbearlab.com/bleshield/ 北欧半导体生产，只支持从模式。 *此款为最烧包的款了。 **费用** 所有使用蓝牙技术的产品都应该通过BQB专利认证，才能拥有蓝牙技术的使用权，当然，国内是比较少人去做的，但使用了蓝牙技术的产品出口到欧盟、美国等一些发达国家，在通关时不能提供这个蓝牙BQB认证，货会被当地海关扣压，因为你的货侵犯了蓝牙的专利权。 蓝牙认证是没有期限的，只要您认证过的产品没有更改它的认证模块及软件版本，就会一直有效，所以，既然没有期限，那么在http://www.bluetooth.org查询得到的产品，都是有效的。 但是，如果您申请蓝牙认证时是以“小企业”的名义去申请BQB认证（即$5000两个QDID，免会员年费），那么第二个QDID的是要在18个月以内申请第二款产品的BQB，如果18月内没申请，那这个QDID就费掉了。BQB的费用初次申请7500USD,以后每年5000USD的会费，针对型号负责。 最新的费用：https://www.bluetooth.org/zh-cn/test-qualification/qualification-overview/fees 能找到的好消息是：所有采用 TI CC254x 蓝牙低功耗系统单晶片 （SoC） 解决方案的使用者皆可使用 TI BLE-Stack 1.2，并可免专利费。找不到BLE不收专利费的消息，猜测是谣传。