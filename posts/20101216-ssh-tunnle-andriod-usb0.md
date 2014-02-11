title: 把手机当无线网卡-andriod usb0 ssh tunnle
link: http://www.54chen.com/_linux_/ssh-tunnle-andriod-usb0.html
author: 54chen
description: 
post_id: 1492
created: 2010/12/16 16:47:18
created_gmt: 2010/12/16 08:47:18
comment_status: open
post_name: ssh-tunnle-andriod-usb0
status: publish
post_type: post

# 把手机当无线网卡-andriod usb0 ssh tunnle

小公司人员增长快，随着台式机日益加多，网速越来越慢，而wifi使用者却还不慢。于是就有了本文。 android手机通过usb共享网络很简单，只需要连上usb线后在手机设置里找到共享手机网络，打开即可。但是本文并非教如何连接usb线的。 ![ssh tunnel](http://img02.taobaocdn.com/imgextra/i2/T1JkFUXotsXXXHh4o2_044309.jpg) 图1 usb绑定设置pc共享android手机上网 ubuntu共享上网后，会发现连接不了线上的ssh，会等半天，报出no ruote。猜测是因为andriod手机的安全设置所致。其实本文重点讲如何ssh的。 先用android下载一个支持ssh的工具：connectbot。 打开软件，同时打开ubuntu，执行下面的语句安装sshd服务： apt-get install openSSL 然后启动sshd服务。 在connectbot中填写ubuntu机器ip，输入密码，ssh到ubuntu机器。 ![connectbot](http://img02.taobaocdn.com/imgextra/i2/T1.6VUXeBEXXafGOk2_044426.jpg) 图2 用connectBot登录pc 这时按下菜单键，选择端口转发。选远端端口，填写2222，转发到192.168.1.1:22，确定以后，这个转发就建立了。 ![connectbot端口转发](http://img03.taobaocdn.com/imgextra/i3/T186VUXfFEXXbkLO.2_044528.jpg) 图3 connectBot端口转发 然后到ubuntu上执行ssh 127.0.0.1 -p2222 ![ssh tunnel](http://img03.taobaocdn.com/imgextra/i3/T15AhUXk4yXXcLijU2_044624.jpg) 图4 我建立的端口转发 回车，输入192.168.1.1的密码，大功告成，成功利用端口转发打通了andriod手机共享usb0共享上网时的ssh。 关于ssh tunnel，详细见：<http://www.54chen.com/_linux_/the-temporary-break-with-the-ssh-tunnel-restriction-cross-room.html>

## Comments

**[wwek](#13274 "2010-12-18 01:45:25"):** ssh tunnle 可是非常的方便。 包括fq什么的。 android系统机准备入了。 wm ppc 用烦了

