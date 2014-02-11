title: gitlab3.1升级4.0手记
link: http://www.54chen.com/_linux_/gitlab3-1-upto-4-notes.html
author: 54chen
description: 
post_id: 2278
created: 2012/12/26 21:59:37
created_gmt: 2012/12/26 13:59:37
comment_status: open
post_name: gitlab3-1-upto-4-notes
status: publish
post_type: post

# gitlab3.1升级4.0手记

在过去的几个星期，已经顺利在gitlab中开始了团队的代码合作。<http://www.54chen.com/_linux_/github-gitlab.html> ![gitlab](http://img04.taobaocdn.com/bao/uploaded/i4/T1L56.XfNeXXasdY75_054835.jpg) 圣诞前后Gitlab发布了最新的4.0版本，感觉最大的改动有： 1.merge request的diff上的comment好用了 2.支持namespace，看上去是未来用来按team划分权限的 3.network界面的bug修复 以下记录了所有的升级过程： 所有过程完全按照https://github.com/gitlabhq/gitlabhq/wiki/From-3.1-to-4.0，任何一步都可以重复搞。 **坑：** 因为在之前由于好奇用管理员账号在后台建立了几个Group，这回namespace支持就坏菜了。 sudo -u gitlab -H bundle exec rake gitlab:enable_namespaces RAILS_ENV=production 会出现一堆错误。由它去吧。 然后所有的原来加的Group的project重新生成push吧。 

> 铁一般的事实说明：一定要先在3.1下把groups设置去掉之后再来打开namespace支持。