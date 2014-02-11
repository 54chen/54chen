title: 百人共用企业maven私服nexus迁移搭建手记
link: http://www.54chen.com/architecture/maven-nexus-notes.html
author: 54chen
description: 
post_id: 2448
created: 2013/11/01 18:35:11
created_gmt: 2013/11/01 10:35:11
comment_status: open
post_name: maven-nexus-notes
status: publish
post_type: post

# 百人共用企业maven私服nexus迁移搭建手记

![nexus](http://chen54.b0.upaiyun.com/1101/nexus200x50.png) 三年前，小米的第一个nexus（版本1.8.0）在一台dell的办公机上安装完成。域名为http://www.a.com。 三年后，a.com时不时已经慢得不行了。特别一到周一，大家都在update snapshot的时候，完全陷入一种无尽的痛苦中。 然后弄来了一台专业server做这事情，域名为http://www.b.com。版本到官网一看，已经2.64了。日新月异！ 看升级文档顿时没了兴趣考虑升级，全新从零安装。 最后的办法是，在新机器上安装新的，把老的仓库挑出来设置为proxy类型。然后启用了ldap，同时保障大家都有deploy权限的同时，最大保障大家的密码不明文出现，同时通过代理ngx来限制最大上传的包，同时规定了snapshot的使用规则，防止在线上使用snapshot。以下是详细记录： **一、下载安装nexus和配置nginx** 找个磁盘分区不小的： wget http://sxrelease.n.miliao.com/nexus-2.6.4-02-bundle.zip unzip nexus-2.6.4-02-bundle.zip bin/nexus start 然后就能用了。http://www.a.com:8081已经启动了。当然了，如果还不能访问，应该是iptables在捣乱，试一下iptables -F。 ［nginx安装忽略］ 然后再在此机器上配置一个nginx代理到8081端口上。只举是为了：1.分担jetty的访问压力，毕竟公司人已经越来越多了。2.好做后续更多的事情。 

server {

listen       80;

server_name  b.com;

location / {

rewrite ^/(.*)$  http://www.b.com/nexus/$1  permanent;

}

access_log  logs/nexus.access.log  main;

location /nexus {

proxy_pass         http://127.0.0.1:8081;

proxy_redirect     off;

proxy_set_header   Host             $host;

proxy_set_header   X-Real-IP        $remote_addr;

proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;

proxy_read_timeout  5m;

client_max_body_size       30m;

}

}

server {

listen       443;

server_name  b.com;

ssl on;

ssl_certificate ../ssl/b.com.crt;

ssl_certificate_key ../ssl/b.com.key;

....

配置中，启用了443的https代理，有安全意识的工程师使用ldap的时候传输密码要安全一点。 proxy_read_timeout设置5分钟，是因为在下载包时，有可能需要去国外拉一些没见过的包，设长一点以防出现504。 client_max_body_size设置30m，如果再有巨大的奇怪包要deploy，不这么容易了，用来防止不明真相的群众往maven库里传乱七八糟的东西。 **二、设置nexus** 第一个设置，为了防止仓库爆仓。 login->scheduled tasks->add，添加一个定时清理目录的计划任务即可。最后一个选项“remove if released”需要慎重，平时开发可能不是特别正规的时候可能会snapshot和release都需要。 第二个设置，打开LDAP支持，让每个人都有权限。 login->security->ldap configuration，填写各种惨无人道的ldap参数，［此处需要求助IT部门］，然后security->roles->add->External Role Mapping，Realm选择LDAP，Role里如果没有数据，说明ldap没设置好，Role里选择一个组，点击create mapping。 然后在configuration中选择add，添加两个权限，一个是Nexus deployment role，一个是All maven repositories (Full control)。 然后在对应组的小伙伴们就有权限了。 **三、设置本地settings.xml** 把原来的settings文件的url从a.com改为b.com，普通的小伙伴即可使用新的仓库了。 要发包的小伙伴，因为ldap用户名密码明文写在文件里不好，于是就用下面的办法： 

## step 1.先随意定义一个种子
    
    
     localhost:54chen 54chen$ mvn --encrypt-master-password 123asdadfafdadf
     {BHe/qKN8q30HBG3bAGbYLOVLnAqVRkzjb9/7yWs+Ks0=}
    
    
     vim ~/.m2/settings-security.xml

写入内容：
    
    
     <?xml version="1.0" encoding="UTF-8"?>
    <settingsSecurity>
       <master>{BHe/qKN8q30HBG3bAGbYLOVLnAqVRkzjb9/7yWs+Ks0=}</master>
    </settingsSecurity>

## step 2.最终生成
    
    
    mvn --encrypt-password 你的邮箱密码
    {RxLx1asdfiafrjIHfXZDadfwveda23avsdv=}

然后修改：
    
    
    vim ~/.m2/settings.xml
    
    
    <server>
         <id>internal</id>
         <username>54chen</username>
         <password>{RxLx1asdfiafrjIHfXZDadfwveda23avsdv=}</password>
    </server>

当然了，mvn的版本要在2.1.0以上才行。 然后对deploy过程中明文传输有顾虑的小伙伴，直接上https即可。 **四、在a.com中存在的包，在b.com中没有怎么办？** 其实很简单，先在a.com中定位404的包在什么repo中，复制下来在a中的repo位置，在b.com中新加一个repo，类型为proxy，同时将remote storage location写成a的。同时， 单击public repositories，调整刚加的repo到ordered repo中去，即可。 ［后记］ 标题有点大，解释一下，百人共用的保障，主要还是在nginx上，挡这一层之后，可以做很多事情，比如再加点cache之类的，实际情况下，所有用户减少过度的snapshot使用，也有一定帮助。