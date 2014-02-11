title: 企业服务器监控及报警配置打造（ganglia and nagios）-part 1
link: http://www.54chen.com/architecture/ganglia-nagios-install.html
author: 54chen
description: 
post_id: 1495
created: 2010/12/09 11:56:54
created_gmt: 2010/12/09 03:56:54
comment_status: open
post_name: ganglia-nagios-install
status: publish
post_type: post

# 企业服务器监控及报警配置打造（ganglia and nagios）-part 1

![ganglia 54chen nagios](http://img04.taobaocdn.com/imgextra/i4/T1G.0TXXlhXXa1KzfX_115619.jpg)

> 系统简介： Ganglia 是 UC Berkeley 发起的一个开源监视项目，设计用于测量数以千计的节点。每台计算机都运行一个收集和发送度量数据（如处理器速度、内存使用量等）的名为 gmond 的守护进程。它将从操作系统和指定主机中收集。接收所有度量数据的主机可以显示这些数据并且可以将这些数据的精简表单传递到层次结构中。正因为有这种层次结构模式，才使得 Ganglia 可以实现良好的扩展。gmond 带来的系统负载非常少，这使得它成为在集群中各台计算机上运行的一段代码，而不会影响用户性能。

我（54chen）的服务器操作系统:centos 5.5 x86_64 （六十四位centOS 5.5） **第一步，修改yum源** # cd /etc/yum.repos.d/ #vim dag.repo 写出如下信息： 

> [dag] name=Dag RPM Repository for Red Hat Enterprise Linux baseurl=http://apt.sw.be/redhat/el$releasever/en/$basearch/dag gpgcheck=1 gpgkey=http://dag.wieers.com/rpm/packages/RPM-GPG-KEY.dag.txt enabled=1

**第二步，通过yum安装所有依赖** yum -y install apr-devel apr-util check-devel cairo-devel pango-devel libxml2-devel \ rpmbuild glib2-devel dbus-devel freetype-devel fontconfig-devel gcc-c++ expat-devel \ python-devel libXrender-devel zlib libpng freetype libjpeg fontconfig gd libxml2 \ pcre pcre-devel libpcre libconfuse libart_lgpl-develyum -y install apr-devel apr-util check-devel cairo-devel pango-devel libxml2-devel \ rpmbuild glib2-devel dbus-devel freetype-devel fontconfig-devel gcc-c++ expat-devel \ python-devel libXrender-devel zlib libpng freetype libjpeg fontconfig gd libxml2 \ pcre pcre-devel libpcre libconfuse libart_lgpl-devel **第三步，通过yum安装rrdtool（画图的数据存储）** yum install -y rrdtool perl-rrdtool rrdtool-develyum install -y rrdtool perl-rrdtool rrdtool-devel **第四步，通过rpm安装libconfuse依赖**

> wget http://download.fedora.redhat.com/pub/epel/5/x86_64/libconfuse-2.5-4.el5.x86_64.rpm wget http://download.fedora.redhat.com/pub/epel/5/x86_64/libconfuse-devel-2.5-4.el5.x86_64.rpm rpm -ivh libconfuse-2.5-4.el5.x86_64.rpm rpm -ivh libconfuse-devel-2.5-4.el5.x86_64.rpm

**第五步，下载源码安装**

> wget http://cdnetworks-kr-1.dl.sourceforge.net/project/ganglia/ganglia%20monitoring%20core/3.1.7/ganglia-3.1.7.tar.gz tar zxvf ganglia-3.1.7.tar.gz cd ganglia-3.1.7 ./configure --with-gmetad --sysconfdir=/etc/ganglia make make install

**第六步，安装 nginx php环境 略去（54chen的老文章里有多个安装nginx-php的文章，自查之。右侧有搜索框） **

> cp -a web/* /var/www/html/ganglia/ cp gmetad/gmetad.init /etc/init.d/gmetad cp gmond/gmond.init /etc/init.d/gmond gmond -t | tee /etc/ganglia/gmond.conf mkdir -p /var/lib/ganglia/rrds chown nobody:nobody /var/lib/ganglia/rrds

**第七步，启动gmond gmetad**

> /etc/init.d/gmond start /etc/init.d/gmetad start

**Q&A** 1.gmetad错误处理 检查 /var/lib/ganglia/rrds 是否建立 以及权限是不是正确 2.配置名称等不生效的原因 gmetad/gmond restart可能会不起作用，直接kill. 3.gmond起不来的原因 udp设置有问题，多播模式，请修改那个默认的ip地址（那是一个不存在的地址，用来广播的，gmeta会从广播里抓出来分析），同一组cluster的机器使用同一个广播ip地址 4.节点安装gmond（客户端）：不再需要rrdtool，不需要gmetad支持 

> rpm -ivh libconfuse-2.5-4.el5.x86_64.rpm rpm -ivh libconfuse-devel-2.5-4.el5.x86_64.rpm yum -y install apr-devel apr-util check-devel cairo-devel pango-devel libxml2-devel \ rpmbuild glib2-devel dbus-devel freetype-devel fontconfig-devel gcc-c++ expat-devel \ python-devel libXrender-devel zlib libpng freetype libjpeg fontconfig gd libxml2 \ pcre pcre-devel libpcre libconfuse libart_lgpl-develyum -y install apr-devel apr-util check-devel cairo-devel pango-devel libxml2-devel \ rpmbuild glib2-devel dbus-devel freetype-devel fontconfig-devel gcc-c++ expat-devel \ python-devel libXrender-devel zlib libpng freetype libjpeg fontconfig gd libxml2 \ pcre pcre-devel libpcre libconfuse libart_lgpl-devel ./configure --prefix=/opt/soft/ganglia --sysconfdir=/opt/soft/ganglia/etc/ make make install /opt/soft/ganglia/sbin/gmond --default_config > /opt/soft/ganglia/etc/gmond.conf

修改cluster_name和多播的那个莫名其妙的ip地址启动即可。 5.安装完成后为什么都正常只有空图却没有显示各节点的数据？ 我（54chen）被这个问题搞了半天，最后才发现是因为各节点的服务器时间有问题， * */2 * * * ntpdate time.windows.com放到crontab里，或者是直接执行一下ntpdate time.windows.com。 6.为什么各个cluster的服务器互相乱窜？ 一定要注意，不同的cluster多播到各个不同的ip去，比如ABCD是一组，那全到239.2.11.71，而FGH是另一组，那就全到239.2.11.72。 7.为什么gmond内外网ip地址乱串？ 在我的集群中，eth0 是我的系统的公共 IP 地址。但是，监视服务器将通过 eth1 与私有集群网络中的节点进行通信。我需要确保 Ganglia 使用的多点传送将与 eth1 绑定在一起。这可以通过创建 /etc/sysconfig/network-scripts/route-eth1 文件来完成。添加 239.2.11.71 dev eth1 内容。 然后您可以使用 service network restart 重新启动网络并确保路由器显示此 IP 通过 eth1。注：您应当使用 239.2.11.71，因为这是 ganglia 的默认多点传送通道。如果使用其他通道或者增加更多通道，请更改它。 8.为什么gmond里输出的xml里什么关键的数据也没有？ 注意看iptables,执行一下iptables -F试试。 下一part讲述nagios搭配ganglia的报警设置。