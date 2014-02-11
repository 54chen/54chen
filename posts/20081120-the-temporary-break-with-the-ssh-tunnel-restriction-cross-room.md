title: 用SSH Tunnel临时突破跨机房的限制
link: http://www.54chen.com/_linux_/the-temporary-break-with-the-ssh-tunnel-restriction-cross-room.html
author: cc0cc
description: 
post_id: 94
created: 2008/11/20 15:04:50
created_gmt: 2008/11/20 07:04:50
comment_status: open
post_name: the-temporary-break-with-the-ssh-tunnel-restriction-cross-room
status: publish
post_type: post

# 用SSH Tunnel临时突破跨机房的限制

[文章作者：陈臻 本文版本：v1.0 最后修改：2008.11.20 转载请注明原文链接：<http://www.54chen.com/c/94>] 今天遇到一个测试机的环境要连通生产机的pgsql,后来用了SSH Tunnel，记录一下： 在hostA中执行 $ssh -N -f -L 9000:hostB:8080 user@hostB N 不用建立shell f 连线后在背景执行 L 连接 这样在hostB中执行localhost:8080相当于访问hostA，也就建立了B到A的信任连接，突破了限制。 算是一句话的[备忘](/c/77)，不过冒似不止一句了。

## Comments

**[jock](#12 "2008-11-20 15:49:02"):** 先顶再看 这个东西平时用不着 要用的时候真有用

