title: cloud foundry之warden代码解读-part2
link: http://www.54chen.com/architecture/cloud-foundry-warden-part2.html
author: 54chen
description: 
post_id: 2235
created: 2012/10/09 17:32:28
created_gmt: 2012/10/09 09:32:28
comment_status: open
post_name: cloud-foundry-warden-part2
status: publish
post_type: post

# cloud foundry之warden代码解读-part2

![warden](http://img.taobaocdn.com/imgextra/i4/13078490/T2UAulXnlbXXXXXXXX_!!13078490.png) **简介** cloudfoundry是vmvare推出来的开源PaaS平台，warden是其核心部分的资源管理容器，完成了各种资源分配的事情。 代码位置在： https://github.com/cloudfoundry/warden **代码结构** |-- em-warden-client 基于eventmachine的client 依赖 warden-client与warden-protocol 通过 unix socket来通讯 |-- warden 巨复杂的warden实现，大部分是shell脚本+ruby，还有两个c实现。 |-- warden-client 客户端驱动，提供与warden的阻塞式通讯client。依赖warden-protocol。其中的V1.rb对各自command进行解析之后的处理。这里有一个v1mode的概念， v1mode使用字符串，转为pb后与其他的一起使用protobuf。 `-- warden-protocol 依赖beefcake(protobuf library for Ruby)。protocol下有所有支持的pb生成格式。 **小坑：ruby中的send方法** send其实就是动态地根据名字调用函数，传递后面的内容作为调用参数，api函数原型为： obj.send(symbol [, args...]) => obj 在V1.rb中，所能的convert_xxxx_request方法都是这样被调用到的。 **专注warden实现** src下四个C程序： |-- clone 用来快速复制一个环境 夹杂一堆的shell在其中运行 |-- iomux 分成两个部分：iomux-spawn把子进程的pid写到stdout来标示它已经做好准备接收连接。在尝试连接之后一直等待。iomux-link是用来根据pid重新link到其上去。 |-- oom 通过eventfd得到内存不够的通知 `-- repquota 报告quota使用情况 四个c程序将在编译后复制到root/linux/skeleton/bin中 **其他目录：** |-- bin 下面有两个脚本文件，用来启动和进入命令行的 |-- config 下面有一个配置文件，定义了linux下的配置 |-- lib 所有的ruby代码 |-- root 各种shell脚本，操作cgroup之类的 **启动warden** 环境： chenzhen@ubuntu-chenzhen:~/warden/warden$ lsb_release -a No LSB modules are available. Distributor ID: Ubuntu Description: Ubuntu 12.04 LTS Release: 12.04 Codename: precise 

> $cd warden/warden $sudo bundle exec rake setup[config/linux.yml] $sudo bundle exec rake warden:start[config/linux.yml] $ctrl+z $bg $sudo bundle exec bin/warden-repl $ping ...pong 

**调用过程：**

> bin/warden-repl >> lib/warden/repl.rb >> Warden::Client >> warden.sock >> lib/warden/server.rb << Warden::Server.run! << Rakefile

**处理调用：**

> server.rb > ClientConnection > receive_request > process(request) > process_container_request(request, container) > base.rb > dispatch(request, &blk;) > send(do_method, request, response, &blk;) -> linux.rb > do_xxx > shell script

**clone.c分析：** src/clone/clone.c: main -> console.c: openpty -> clone.c: daemonize -> barrier.c: barrier_open -> clone.c: run("./hook-parent-before-clone.sh"); -> clone.c: parent_clone_child -> clone.c: pid = clone(start, stack, flags, h); -> clone.c: start() -> console_mount(&h-;>console, "dev/console"); -> pivot_root -> clone.c: run("./hook-parent-after-clone.sh"); -> clone.c: barrier_signal(&h-;>barrier_parent); -> clone.c: barrier_wait(&h-;>barrier_child);