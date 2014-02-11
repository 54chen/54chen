title: scribe的生产实践总结
link: http://www.54chen.com/_linux_/scribe-on-production.html
author: 54chen
description: 
post_id: 2311
created: 2013/04/10 17:53:06
created_gmt: 2013/04/10 09:53:06
comment_status: open
post_name: scribe-on-production
status: publish
post_type: post

# scribe的生产实践总结

![scribe](http://img02.taobaocdn.com/imgextra/i2/T1a5J6XmhtXXbWyPE9_103845.jpg) **一、scribe是什么** scribe是facebook开源的分布式日志系统。这里两年前的使用手记。<http://www.54chen.com/_linux_/log-server-scribe-helper.html>。严格说它只是一个日志处理的节点，与分布式系统相比差很多复杂的节点间的控制过程。不过也贵在它很精简，不容易出现问题。两年多时间以后，线上还没出现过一例scribed自己crash掉的情况。 **二、scribe遇到master挂掉** 生产环境中一般配置为一个primary(type=network)带一个secondary(type=file)，在遇到master挂掉的情况下，会直接写到本地磁盘。 这个设置有非常多好处，当远程的master恢复了，本地磁盘的这些个日志还会被scribe给送回去。 日常要监控这个事情的发生也很简单，一行脚本就可以： `ALERT=`du -sb /tmp/scribe/|awk '{print $1}'` if [ ${ALERT} -ge 1024000 ]; then //alert fi` **三、scribe自己挂掉** scribe自己挂掉要预防把业务堵死了，一定要用异步线程在写这个日志，如果本地的scribed挂了，那端口一定不在了，在new thrift连接的时候就会失败，可以在这个异步线程中去设定多久再重试，但这就是有损日志的了。 日常监控一般可以直接看端口还在不在，不过一般不会挂（两年多还没见过）。 比较文艺的做法可以用他给的ctrl.sh那个python脚本去读取scribed内部的perfcounter来报警。 **四、scribe遇到master没挂但是网络不好** 这个比较难受，网络一堵，很多天都追不上来。而且会出现恶性循环。所以还是要网络给力，最差要能传输不滞留日志。 我米高人航军大侠搞了一个压缩过的传输scribed改造，还没试用，理论上数据量降了应该有一定缓解。 **五、scribe与kafka对比** kafka是linkedin开源的一个日志分发系统，scala的，很牛B的一个系统，以cluster、去中心化、高效为特点，比起scribed来说，kafka是航空母舰，scribed是激流勇进那种冲锋舟，各有各的好处。 从日志收集的需求来看，如果遇到前面4点的故障的话，使用kafka的话，要做的事情还不少。但如果出现中心化的故障的话，还是kafka不怕down机。