title: riak源码阅读手记 操作列表及supervisorTree一览
link: http://www.54chen.com/document/riak-source-notes-operation-supvisor-detail.html
author: 54chen
description: 
post_id: 1869
created: 2011/10/18 16:07:22
created_gmt: 2011/10/18 08:07:22
comment_status: open
post_name: riak-source-notes-operation-supvisor-detail
status: publish
post_type: post

# riak源码阅读手记 操作列表及supervisorTree一览

基于0.14.2的记录，1.0的变化会在后续记录。 ![riak](http://wiki.basho.com/images/logo_wiki.png) riak源码阅读手记一 初出茅庐 项目入口 <http://www.54chen.com/_linux_/riak-source.html> riak源码阅读手记二 左右开弓 启动 <http://www.54chen.com/_linux_/riak-cource-code.html> riak源码阅读手记 运行安装 <http://www.54chen.com/_linux_/riak-source-install-run.html> 一些常用的指令： <http://wiki.basho.com/Command-Line-Tools.html#riak-admin> riak源码阅读手记 压力测试 <http://www.54chen.com/_linux_/riak-benchmark.html> riak源码阅读手记 操作列表及supervisorTree一览 <http://www.54chen.com/_linux_/riak-source-notes-operation-supvisor-detail.html> **[山寨收集操作列表]** 看系统状况，有各种内存cpu硬盘的情况 bin/riak-admin status 节点更换新的ip地址： 如果是所有的节点都换，那在停下来集群，在每个节点都依次运行: ./riak-admin reip riak@192.168.103.9 riak@10.235.2.226 ./riak-admin reip riak@192.168.97.48 riak@10.235.2.183 ./riak-admin reip riak@192.168.100.52 riak@10.235.2.138 依次去对应的节点，修改etc: 

  1. sed -i 's/192.168.103.9/10.235.2.226/g' app.config  
  2. sed -i 's/192.168.103.9/10.235.2.226/g' vm.args  
  3.   
  4. sed -i 's/192.168.97.48/10.235.2.183/g' app.config  
  5. sed -i 's/192.168.97.48/10.235.2.183/g' vm.args  
  6.   
  7. sed -i 's/192.168.100.52/10.235.2.138/g' app.config  
  8. sed -i 's/192.168.100.52/10.235.2.138/g' vm.args   

进入运行着的集群： ./riak attach ctrl+d退出 看ring的分布情况： >{ok,Ring} = riak_core_ring_manager:get_my_ring(). **[看一点代码]** riak_kv_app -> riak_core->riak_core_sup[supervisor]-> riak_core_sup |-riak_core_handoff_manager handoff管理 gen_server |-riak_core_handoff_listener handoff接收 gen_nb_server |-riak_core_ring_events 处理ring变化 gen_event |-riak_core_ring_manager ring管理 gen_server2 |-riak_core_node_watcher_events 节点侦察 gen_event |-riak_core_node_watcher 节点 gen_server |-riak_core_gossip gossip协议 gen_server **[riak里的自定义behaviour]** -gen_nb_server from gen_server -gen_server2 gen_server的一个拷贝 增加了pxxx的办法及增加了许多参数 -riak_core_vnode from gen_fsm riak_kv: -riak_kv_backend Riak backend behaviour