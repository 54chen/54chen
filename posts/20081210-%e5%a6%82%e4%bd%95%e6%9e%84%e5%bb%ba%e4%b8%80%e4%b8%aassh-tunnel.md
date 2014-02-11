title: 如何构建一个ssh tunnel
link: http://www.54chen.com/life/%e5%a6%82%e4%bd%95%e6%9e%84%e5%bb%ba%e4%b8%80%e4%b8%aassh-tunnel.html
author: cc0cc
description: 
post_id: 274
created: 2008/12/10 10:55:58
created_gmt: 2008/12/10 02:55:58
comment_status: open
post_name: %e5%a6%82%e4%bd%95%e6%9e%84%e5%bb%ba%e4%b8%80%e4%b8%aassh-tunnel
status: private
post_type: post

# 如何构建一个ssh tunnel

# 摘要

本文介绍了如何使用ssh tunnel的方式访问内网数据。 

# [[编辑](http://10.62.164.49/wiki/index.php?title=%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AAssh_tunnel&action=edit&section=2)] 命题

我希望把内网机器A的端口a，通过外网机器B的端口b转发出去，让外网可以直接访问内网机器A的端口a。 我有一台机器A，我可以访问；同时有一台机器B；甲可以访问；但是甲无法访问A，问：如何能让甲可以访问机器A？这个问题很痛苦，别慌，ssh 可以帮助你，只要你满足一定的前提条件即可。 

# [[编辑](http://10.62.164.49/wiki/index.php?title=%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AAssh_tunnel&action=edit&section=3)] 前提

  1. 你在A和B上都有账号（可以ssh的）
  2. 你有一台机器C，可以同时访问A和B
  3. 你可以有办法修改外网B机器的sshd配置（或者可以启动一个独立的sshd）

# [[编辑](http://10.62.164.49/wiki/index.php?title=%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AAssh_tunnel&action=edit&section=4)] 解法

## [[编辑](http://10.62.164.49/wiki/index.php?title=%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AAssh_tunnel&action=edit&section=5)] 打开ssh的gateway ports

编辑B机器的 ssh 配置文件，一般是 /etc/ssh/sshd_config，设置如下行： 
    
    
     GatewayPorts yes

然后重启 sshd，最通常的做法是： 
    
    
     /etc/init.d/sshd restart

或者 slackware的： 
    
    
     /etc/rc.d/rc.sshd restart

## [[编辑](http://10.62.164.49/wiki/index.php?title=%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AAssh_tunnel&action=edit&section=6)] 建立tunnel

在C机器（其实在A机器上也可以的）上执行下面命令： 
    
    
      ssh -q -x -R b:A:a username@B

## [[编辑](http://10.62.164.49/wiki/index.php?title=%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AAssh_tunnel&action=edit&section=7)] 对付笨蛋的网关

很多网关的keepalive时间非常短，短于ssh自己的keepalive的probe，因此如果连接上去之后一段时间不动，连接就会中断，于是tunnel也断了，对付之的最简单方法就是让tunnel里头时不时地有点数据，我的做法是： 
    
    
     ssh -q -x -R b:A:a username@B vmstat 60

这样，跑起来之后，会每隔60秒输出一次 vmstat 的结果，于是tunnel就“一直”处于忙的状态了。于是就不会断了。 

## [[编辑](http://10.62.164.49/wiki/index.php?title=%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AAssh_tunnel&action=edit&section=8)] 如何让自己退出登录的ssh窗口

假如我是ssh到C机器上执行上面那行命令的怎么办？我的PC岂不是不能关了？因为一关掉就会断掉ssh命令啊。 放心吧，unix/linux里头啥都有，我们用它的screen命令做一个虚拟终端： 
    
    
      screen ssh -q -x -R b:A:a username@B vmstat 60

这样，运行起来之后，可以安全地关闭这个窗口，回头，你可以用 
    
    
      screen -ls

找回你的虚拟终端，然后杀掉或者重建等等。 screen 的用处可不仅仅这些哦，假如你通过易断的网络干一件常时间执行的事情，最好前面带上screen ;)