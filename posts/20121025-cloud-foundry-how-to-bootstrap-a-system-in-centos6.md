title: cloud foundry - how to bootstrap a system in centos6
link: http://www.54chen.com/architecture/cloud-foundry-how-to-bootstrap-a-system-in-centos6.html
author: 54chen
description: 
post_id: 2246
created: 2012/10/25 19:16:35
created_gmt: 2012/10/25 11:16:35
comment_status: open
post_name: cloud-foundry-how-to-bootstrap-a-system-in-centos6
status: publish
post_type: post

# cloud foundry - how to bootstrap a system in centos6

![bootstrap](http://img.taobaocdn.com/imgextra/i4/13078490/T2UAulXnlbXXXXXXXX_!!13078490.png) **1)install febootstrap** Febootstrap is a tool like debootstrap in ubuntu. #yum -y install febootstrap #febootstrap centos6 rootfs http://mirror.neu.edu.cn/centos/6.3/os/x86_64/ Threre is a basic centos system in rootfs dir. **2)install ruby 1.9 (optional)** yum -y groupinstall "Development Tools" yum -y install openssl-devel zlib-devel gcc gcc-c++ make autoconf readline-devel curl-devel expat-devel gettext-devel readline-devel wget http://pyyaml.org/download/libyaml/yaml-0.1.4.tar.gz tar xzvf yaml-0.1.4.tar.gz cd yaml-0.1.4 ./configure --prefix=/opt/soft/yaml make && make install wget http://ruby.taobao.org/mirrors/ruby/ruby-1.9.3-p286.tar.gz tar xzvf ruby-1.9.3-p286.tar.gz cd ruby-1.9.3-p286 ./configure --prefix=/opt/soft/ruby --enable-shared --disable-install-doc --with-opt-dir=/opt/soft/yaml make && make install ln -s /opt/soft/ruby/bin/* /usr/bin/ gem source -r http://rubygems.org/ gem source -a http://ruby.taobao.org gem install bundler --no-rdoc --no-ri gem install rails --no-rdoc --no-ri ln -s /opt/soft/ruby/bin/* /usr/bin/ **3)install and start cgroups (optional)** All of the configuration is here: vi /etc/cgconfig.conf Turn it on: chkconfig --level 3 cgconfig on chkconfig --list service cgconfig start **4)turn off selinux** vi /etc/selinux/config Change SELINUX=enforcing to SELINUX=disabled Reboot or run: setenforce 0 **5)supper tool: ip** in ssh1: ip link add name FOO type veth peer name BAR ip link set FOO netns 1 ifconfig FOO 10.254.0.222 netmask 255.255.255.252 up ip link set BAR netns  route add -host 10.254.0.221 dev FOO in ssh2: unshare -n /bin/bash ifconfig BAR 10.254.0.221 netmask 255.255.255.252 up ifconfig lo up in ssh1: ping 10.254.0.221 \--------------EOF------------------ Ha ha, expect my next time write again, write here today, very tired of.