title: cloud foundry之dea代码解读
link: http://www.54chen.com/architecture/cloud-foundry-dea.html
author: 54chen
description: 
post_id: 2215
created: 2012/09/07 15:02:30
created_gmt: 2012/09/07 07:02:30
comment_status: open
post_name: cloud-foundry-dea
status: publish
post_type: post

# cloud foundry之dea代码解读

![cloudfoundry](http://img.taobaocdn.com/imgextra/i4/13078490/T2UAulXnlbXXXXXXXX_!!13078490.png) **简介** cloudfoundry是vmvare推出来的开源PaaS平台，dea（DropletExecution Agent）是其应用运行的环境，一个DEA可以启动多个应用。 代码位置在： https://github.com/cloudfoundry/dea 这一部分也是我最想了解细节的地方，因为在一个paas平台中，资源的隔离才是最有价值的部分。 **历史** 初始版的dea基本不控制系统资源，然后基于cgroup搞了一个warden（真正的资源隔离是它，下节再述），程序运行容器。cf希望warden运行在多个平台上，所以没有直接使用LXC。 **代码** 代码入口在lib/dea.rb: 

> EM.epoll EM.run { agent = DEA::Agent.new(config) agent.run() }

EM是ruby有名的eventmachine，是一个事件驱动网络框架，从java程序员的角度，可以理解有点像mina，也可以认为有点像erlang的gen_server。 然后进到lib/dea/agent.rb: 

> NATS.start(:uri => @nats_uri) do ...

里面注册了各种各样的收到对应消息干什么活的定义，我们来看其中几条线： 1\. 收到update: NATS.subscribe('dea.update') { |msg| process_dea_update(msg) } 上一句是说收到update的指令， process_dea_update里面关键的一句： register_instance_with_router(instance, :uris => (uris - current_uris)) 通过NATS告诉router，新的app来了。 2.收到start: NATS.subscribe("dea.#{uuid}.start") { |msg| process_dea_start(msg) } 在process_dea_start里面： start_operation = lambda do... 把app下载回来，修改好权限什么的。 下面用了fiber（它是ruby里的协程，用来达到异步线程的效果，但是开销要比线程低）： f = Fiber.new do.. f.resume 在协程中，最关键的调用： stage_app_dir(bits_file, bits_uri, sha1, tgz_file, instance_dir, runtime) bind_local_runtime(instance_dir, runtime) startup_contents.gsub!('%VCAP_LOCAL_RUNTIME%', runtime['executable']) // 替换运行时路径 File.open(startup, 'w') { |f| f.write(new_startup) } #写入运行的脚本 一切就绪之后，调用先前准备好的lambda: start_operation.call 然后去运行各种shell命令，包括启动应用。 __EOF__