title: riak源码阅读手记二 左右开弓 启动
link: http://www.54chen.com/document/riak-cource-code.html
author: 54chen
description: 
post_id: 1801
created: 2011/08/24 10:28:57
created_gmt: 2011/08/24 02:28:57
comment_status: open
post_name: riak-cource-code
status: publish
post_type: post

# riak源码阅读手记二 左右开弓 启动

![erlang riak 54chen](http://img04.taobaocdn.com/imgextra/i4/T1_EN.XadjXXbk3DE3_051340.jpg) **目标系统(target system)** OTP系统定义里有这么一个东西： 可以被一般的erl脚本启动的系统叫基础目标系统； 除此之外还可以做运行时代码替换的系统叫简单目标系统； 如果还支持日志输出到文件、自动定时启动的话就叫做内嵌目标系统。 **启动目标系统的方式** 通过erlang/OTP标准的reltool工具生成的目标系统，可以用多种方式灵活启动。 第一种： 

> os> /usr/local/erl-target/bin/erl

这只启动了一个基础目标系统。基本没什么具体功能。 第二种： 

> os> /usr/local/erl-target/bin/erl -boot /usr/local/erl-target/releases/FIRST/start

用-boot参数，这可以启动一个简单目标系统。releases/RELEASES文件可用来做热替换。 第三种： 

> bin/start 

这个脚本会去调用 bin/run_erl ，最终调用 bin/start_erl启动。run_erl是一个提供运行时日志输出到文件的封装。还提供了简单的机制attach到一个erlang shell。 **自定义behaviour**

> -spec behaviour_info(atom()) -> 'undefined' | [{atom(), arity()}]. behaviour_info(callbacks) -> [{start,2}, % (Partition, Config) {stop,1}, % (State) {get,2}, % (State, BKey) {put,3}, % (State, BKey, Val) {list,1}, % (State) {list_bucket,2}, % (State, Bucket) {delete,2}, % (State, BKey) {drop,1}, % (State) {fold,3}, % (State, Folder, Acc), Folder({B,K},V,Acc) {is_empty,1}, % (State) {callback,3}]; % (State, Ref, Msg) -> behaviour_info(_Other) -> undefined.

上述就是一个自定义的behaviour，使用的时候： 

> -behaviour(xxx). ...

有点类似java的interface.