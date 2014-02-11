title: ubuntu9.10里的firefox不显示DNS Flusher插件的解决办法
link: http://www.54chen.com/_linux_/ubuntu910-inside-firefox-does-not-display-plug-in-solution-to-dns-flusher.html
author: cc0cc
description: 
post_id: 839
created: 2009/11/23 16:18:53
created_gmt: 2009/11/23 08:18:53
comment_status: open
post_name: ubuntu910-inside-firefox-does-not-display-plug-in-solution-to-dns-flusher
status: publish
post_type: post

# ubuntu9.10里的firefox不显示DNS Flusher插件的解决办法

ubuntu 9.10里的firefox是3.55版本的，DNS Flusher是个很好用的快速切换dns的插件。 ubuntu下是大小写敏感的，而DNS Flusher的作者似乎只是个windows用户。 

> 修改这个文件： vim /home/cc/.mozilla/firefox/icj6nr4a.default/extensions/{7d575baa-b543-11dc-8314-0800200c9a66}/chrome.manifest

(根据你的情况自己修改下目录) 然后修改这个文件里的第一行和第二行里的两个overlay，将里面的**dnsFlusher.xul**都改成**dnsflusher.xul**，注意，只修改前面两个overlay，后面的content和skin无需修改。