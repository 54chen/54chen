title: NFS使用记录
link: http://www.54chen.com/_linux_/nfs-usage-records.html
author: admin
description: 
post_id: 32
created: 2006/11/17 18:13:03
created_gmt: 2006/11/17 10:13:03
comment_status: open
post_name: nfs-usage-records
status: publish
post_type: post

# NFS使用记录

第一步：#serviceconf 第二步：启动portmap和nfs(每次修改exports都要重启nfs才会生效) 第三步：#vi /etc/exports 第四步：/root 202.199.159.193(rw) option: ro 只读 rw 读写 noaccess 无权 第五步：在202.199.159.193这个IP的机器上，#mount 10.10.138.192:/root cc0cc/(如果不行可以试试把10.10.138.192的iptables关掉) 提示：由于它的认证机制是基于IP地址的,因而容易被ip-spoofing攻击.一般除非必要不要打开这项服务.

## Comments

**[BackingSecond](#10651 "2009-07-07 19:04:44"):** version3 不予评论 平时玩玩就好

