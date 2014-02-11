title: linux系统更换sshd的方法手记[from科学院]
link: http://www.54chen.com/_linux_/linux-system-replacement-method-sshd-notes-from-from-academy-of-sciences.html
author: cc0cc
description: 
post_id: 821
created: 2009/11/04 15:42:12
created_gmt: 2009/11/04 07:42:12
comment_status: open
post_name: linux-system-replacement-method-sshd-notes-from-from-academy-of-sciences
status: publish
post_type: post

# linux系统更换sshd的方法手记[from科学院]

朋友的服务器让人给黑了，把sshd都给更换了，拿到root用户名密码直接用curl往外传。绝对是老毛子的手法，非常娴熟，入侵涉及的方案有[perl](/79-perl-workshop-2008-%e5%bd%92%e6%9d%a5/)服务、c、shell、curl、php exec等等。 在最简单的分析下，采取更换sshd服务的办法来去除老毛子的垃圾sshd. 第一步、下载、安装 

> wget -c "ftp://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-5.0p1.tar.gz" tar zxvf openssh-5.0p1.tar.gz cd openssh-5.0p1

请确认Zlib和OpenSSL的版本: openssh-5.0p1要求Zlib的版本在1.2.1.2以上,[OpenSSL](/809-tsinghua-huambo-fist-of-fury-review-environmental-elements-lamp/)版本在0.9.6以上.下面是Zlib和 OpenSSL的官方地址: http://www.gzip.org/zlib/ http://www.openssl.org/ 在安装过程中我遇到了zlib的版本过低。从上边的网址去下一个 zlib-1.2.3.tar.gz 

> tar zxvf zlib-1.2.3.tar.gz cd zlib-1.2.3 ./configure make && make install

然后重新进入 openssh-5.0p1: 

> ./configure --sysconfdir=/etc/ssh make 这一步后面很牛B，要去到那个修改过的openssh的目录里 make uninstall (就是要把老毛子的病毒sshd给全部rm掉),然后再执行下面的步骤： make install

第二步、配置启动革新 新安装的sshd都在/usr/local/sbin/sshd，老的在/usr/sbin/sshd，ps能看到老的还在跑（当然还在跑，不跑你还能连着ssh操作吗~~·） 修改配置文件，让新的sshd在新的端口启动： 

> vi /etc/ssh/sshd_config Port 220 然后启动新的sshd: /usr/local/ssh/sbin/sshd -f /etc/ssh/sshd_config 注意打开下iptable: iptables -I [INPUT](/230-%e6%b7%ab%e8%8d%a1%e4%b8%80%e4%b8%8b%ef%bc%8c%e4%bd%a0%e5%b0%b1%e7%9f%a5%e9%81%93/) -p udp --dport 220 -j ACCEPT

K掉原来的~~搞定！打完收工。

## Comments

**[muxueqz](#12045 "2009-12-10 13:23:37"):** 嗯，果然很邪恶。

