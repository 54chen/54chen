title: 企业github之高仿选择－gitlab使用实录
link: http://www.54chen.com/_linux_/github-gitlab.html
author: 54chen
description: 
post_id: 2266
created: 2012/11/23 17:38:31
created_gmt: 2012/11/23 09:38:31
comment_status: open
post_name: github-gitlab
status: publish
post_type: post

# 企业github之高仿选择－gitlab使用实录

![gitlab](http://img04.taobaocdn.com/bao/uploaded/i4/T1L56.XfNeXXasdY75_054835.jpg) 自从上周使用git-svn以来（<http://www.54chen.com/_linux_/git-svn.html>），一发不可收拾，于是在偶然的机会发现了gitlab这个不错的选择（可以想象成是github的公司自己搭建的内部版本），还有什么理由继续使用svn+redmine+wiki! 

> @有个梨UGlee 一个人，20年，单枪匹马的干翻了整个计算工业。如果你是程序员，还没有在他写的操作系统上工作，或者还没有开始使用他缔造的版本管理系统，那么你还是趁早改行吧，你干这行是没希望的。

**安装** 用ubuntu，让生活更加轻松。 

> root@ubuntu-chenzhen:/home/gitlab/gitlab# lsb_release -a No LSB modules are available. Distributor ID: Ubuntu Description: Ubuntu 12.04 LTS Release: 12.04 Codename: precise

请打开以下地址并收藏：<https://github.com/gitlabhq/gitlabhq/blob/stable/doc/install/installation.md> 所有的安装只需要一步一步按照说明做就可以完成。以下记录一些中国特色的安装过程。 **中国特色** 1.安装ruby1.9.3p0 不要下了，打不开的（原因你懂的）。 直接 apt-get install ruby1.9.3 2.补充安装bundler 装完ruby就有gem了，然后把源改在淘宝： gem source -r http://rubygems.org/ gem source -a http://ruby.taobao.org gem install bundler --no-rdoc --no-ri gem install rails --no-rdoc --no-ri 3.所有有bundle命令的执行都要注意 在执行目录下，要修改Gemfile文件头的rubygems.org到ruby.taobao.org，否则是不工作的，原因都懂的。 **LDAP登录gitlab** vim config/gitlab.yml 配置在 

> # 2. Auth settings # ========================== ldap:

当你安装好打开的时候，一定会惊呼：还有什么理由继续使用svn+redmine+wiki!

## Comments

**[caixw](#15499 "2013-04-26 09:43:45"):** 这2天一直在配这个东西，受教了

