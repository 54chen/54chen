title: squid缓存失效之谜：一步步提高squid缓存命中率办法记录
link: http://www.54chen.com/web-ral/squid-tcp-miss-200.html
author: 54chen
description: 
post_id: 1482
created: 2010/11/30 13:44:10
created_gmt: 2010/11/30 05:44:10
comment_status: open
post_name: squid-tcp-miss-200
status: publish
post_type: post

# squid缓存失效之谜：一步步提高squid缓存命中率办法记录

手里有个前端自己做cdn的服务器，结果发现进出流量相等，这是个诡异的事情，因为一般来说，做前端缓存的机器，流量基本上很长时间里都是出多进少的。 一开始的时候，是nginx做的nginx_cache，发现进出流量相等后，换上了squid 3.0，使用命令： squidclient -p 8088 mgr:info （启在8088） 发现如图所示的几个命中率，相当低下，一般的WEB服务器缓存30－60％是正常的，而CDN作用的应该说80%以上才是正常的。 ![http://img03.taobaocdn.com/imgextra/i3/T1nU8SXdFrXXcxpD.U_015909.jpg](http://img03.taobaocdn.com/imgextra/i3/T1nU8SXdFrXXcxpD.U_015909.jpg) 刚启动squid的时候，个人认为是因为缓存文件还没有拖动到前端来，所以是正常的，但结果过了一天后，还是没有看到进流量松动。 第二天来，硬盘已经过了10G了，还是不行。通过cat access.log来看，cat access.log|gawk '{print $4}'|sort|uniq -c|sort -nr 发现TCP_MISS/200相当的多，再看流量图，进出还是没有变化。杯具。 于是乎，想到了直接tail -f access.log，看看究竟都有什么东西在访问。 一条接一条的xxx.jpg?unid=1111触动了我（公司用来做用户行为分析的参数），就这样白花花的浪费了缓存。 **解决方案：** 幸好，squid前面，还有一层nginx，于是考虑在nginx上做转发，将所有的xxx.jpg?xx=xx全部转到 xxx.jpg去，过滤掉url中的参数。尝试了几个不行，内置的参数$url $request_url都不可行。 只有一招利用403来跳转的可行。 

> location ~* (.jpg$)|(.png$)|(.gif$)|(.mp3$)|(.txt$){ proxy_pass http://squid; proxy_redirect default; if ($is_args) { return 403; error_page 403 =200 $scheme://$host$uri; } } 

重启nginx，看到了进流量图明显大幅度下降。 ![http://img02.taobaocdn.com/imgextra/i2/T14F8TXolXXXXOWxsV_020704.jpg](http://img02.taobaocdn.com/imgextra/i2/T14F8TXolXXXXOWxsV_020704.jpg)

## Comments

**[spark](#13245 "2010-12-01 16:53:28"):** 行为分析有必要用一个图片来做吗？

**[54chen](#13246 "2010-12-01 17:53:13"):** 历史原因，总有一些人一直土着，而且不愿意改变。

**[tangfl](#13247 "2010-12-02 10:03:51"):** 那这个行为分析的功能岂不就无效了？

**[54chen](#13248 "2010-12-03 11:47:27"):** 还好，还在打日志

**[sk](#13504 "2011-04-14 11:17:53"):** 这样完全没什么效果，不如直接把动态页面转向

**[aaa](#13950 "2011-11-02 00:26:35"):** 为什么不加一个 \\.(jpg|png|gif|mp3|txt)\?的缓存规则？

