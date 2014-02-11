title: 当Pg连接数达到一定数量时，Pgsql进程会终止，报Out of memory
link: http://www.54chen.com/_linux_/%e5%bd%93pg%e8%bf%9e%e6%8e%a5%e6%95%b0%e8%be%be%e5%88%b0%e4%b8%80%e5%ae%9a%e6%95%b0%e9%87%8f%e6%97%b6%ef%bc%8cpgsql%e8%bf%9b%e7%a8%8b%e4%bc%9a%e7%bb%88%e6%ad%a2%ef%bc%8c%e6%8a%a5out-of-memory.html
author: cc0cc
description: 
post_id: 101
created: 2008/11/21 14:36:49
created_gmt: 2008/11/21 06:36:49
comment_status: open
post_name: %e5%bd%93pg%e8%bf%9e%e6%8e%a5%e6%95%b0%e8%be%be%e5%88%b0%e4%b8%80%e5%ae%9a%e6%95%b0%e9%87%8f%e6%97%b6%ef%bc%8cpgsql%e8%bf%9b%e7%a8%8b%e4%bc%9a%e7%bb%88%e6%ad%a2%ef%bc%8c%e6%8a%a5out-of-memory
status: publish
post_type: post

# 当Pg连接数达到一定数量时，Pgsql进程会终止，报Out of memory

Q： 当Pg连接数达到一定数量时，Pgsql进程会终止，报Out of memory A：在 Linux 2.4 以及之后的版本里，缺省的虚拟内存的设置对于 PgSQL而言不是最优的(32M)。原因也可能在于内核实现内存提交的方法导致出现了“过提交”内存（具体情况由于没有遇到，不作评述），如果其它进程的内存请求导致系统用光虚拟内存，那么内核可能会立即终止 PGSQL 服务器进程并会出具Out of memory报错，唯一的解救方法只能手工kill掉数据库进程，重启数据库。修改方法为： 
    
    
    sysctl -w kernel.shmmax=134217728
    sysctl -w kernel.shmall=2097152
    sysctl -w vm.overcommit_memory=2

或者直接echo进proc中去（我个人喜欢比较传统的操作） 
    
    
    echo 134217728 >/proc/sys/kernel/shmmax
    echo 2097152 >/proc/sys/kernel/shmall
    echo 2 > /proc/sys/vm/overcommit_memory

考虑到并不是每个人都乐意于每次开机都作此操作，最好还是修改/etc/sysctl.config文件添加相应的设置。 

### Slony不同步数据？ 

Q：配置了Slony,并未报错，但数据没同步过来 A：你要同步的表都设置主键了么，如果没有设主键的Slony配置表的属性的时候要加Serial属性哦 

### Slony同步数据非常慢？ 

Q：Slony同步数据非常慢，需要20~30s，且配置Slony的机器上cpu消耗严重？ A：检查slony同步的时间间隔，默认是10000微妙，这个数值还不错，别设置太低了。