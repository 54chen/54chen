title: cloud foundry之warden代码解读-part1
link: http://www.54chen.com/architecture/cloud-foundry-warden-part1.html
author: 54chen
description: 
post_id: 2220
created: 2012/09/14 18:49:19
created_gmt: 2012/09/14 10:49:19
comment_status: open
post_name: cloud-foundry-warden-part1
status: publish
post_type: post

# cloud foundry之warden代码解读-part1

![warden](http://img.taobaocdn.com/imgextra/i4/13078490/T2UAulXnlbXXXXXXXX_!!13078490.png) **简介** cloudfoundry是vmvare推出来的开源PaaS平台，warden是其核心部分的资源管理容器，完成了各种资源分配的事情。 代码位置在： https://github.com/cloudfoundry/warden 这一部分也是我最想了解细节的地方，因为在一个paas平台中，资源的隔离才是最有价值的部分。 **基础知识**

> rv = unshare(CLONE_NEWNS);

unshare这个调用，可以把挂载的文件系统设置成只在新的挂载命名空间（mount namespace）中可见。 

> execvp(argv[0], argv);

execvp()会从PATH 环境变量所指的目录中查找符合参数file 的文件名，找到后便执行该文件，然后将第二个参数argv传给该欲执行的文件。 

> shopt -s nullglob

设置shell环境变量nullglob的值为on，nullglob为on时对于通配符匹配时，若匹配不到时为空（相对应的为通配符本身）。 

> int stat(const char *restrict pathname, struct stat *restrict buf);

提供文件名字，获取文件对应属性。 

> build-essential软件包

作用是提供编译程序必须软件包的列表信息，也就是说编译程序有了这个软件包，它才知道 头文件在哪，才知道库函数在哪，还会下载依赖的软件包，最后才组成一个开发环境。 

> 工具debootstrap

可以用于在系统的某个目录中安装一套基本系统，这个基本系统除了一些配置项外，与ubuntu安装程序在安装的第一阶段安装的内容基本相同。这项功能有许多有趣的功能，例如，你可以从某个定制版本的ubuntu Live光盘上通过这个命令快速的在硬盘上安装ubuntu而不需要ubuntu的安装程序，也可以把创建在硬盘上的基本系统目录作为某些涉及系统安全性服务的chroot运行环境，通过chroot进入该目录并调试和运行一些可能修改系统配置的应用程序，作为定制小型系统模板等等。 

> aufs

一种文件格式，可以mount到目录，同时控制只读和读写。 

> overlayfs

另一种文件格式，在ubuntu 11.04后开始替代aufs作为官方livecd的文件格式。