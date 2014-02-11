title: 又拍云cdn试用体验
link: http://www.54chen.com/web-ral/upai-cdn-test.html
author: 54chen
description: 
post_id: 2428
created: 2013/10/08 20:07:35
created_gmt: 2013/10/08 12:07:35
comment_status: open
post_name: upai-cdn-test
status: publish
post_type: post

# 又拍云cdn试用体验

![54chen又拍云试用体验报告](http://chen54.b0.upaiyun.com/1008/wallpaper_1282030786.jpg) 十一之前，又拍云存储（upyun.com）市场部的谢静同学邮件我，让我试用又拍云的功能。之前在infoq或者是之类的技术讲座上有看到过又拍云的报道和技术架构讲解，印象还是不错的，本着支持创业提升期企业的态度，特写此文写下全部试用感受。 **用法** 我的用法比较简单，因为diahosting给我提供的vps在国外，流量是个大问题，所以写博客时，基本都不上传大图片。遇到要上传的图片，之前基本都是两个地方，一是淘宝（嘿嘿，提醒淘宝的兄弟不要把那个上传的口关了哈，淘宝的cdn岗岗的！），二是新浪微博。 上述两个老办法的坏处：淘宝的话，每次上传打开步骤都老麻烦了，又是密码又是选上架之类的。微博呢，又老有那个水印。二者图片质量都不行，老是压缩得不行了。 申请了又拍云存储之后，直接使用它的ftp功能，无压缩，cdn速度也岗岗的。<http://www.54chen.com/architecture/how-to-document-code.html> 此文中第一个图即为又拍云提供。 使用记录（mac下纯ftp命令记录）： 

> localhost:~ chenzhen$ mkdir -p 54chen-pic/1008 localhost:~ chenzhen$ cd 54chen-pic/1008 localhost:1008 chenzhen$ cp ~/Downloads/wallpaper_1282030786.jpg . localhost:1008 chenzhen$ ls wallpaper_1282030786.jpg localhost:1008 chenzhen$ ftp v2.ftp.upyun.com Connected to v2.ftp.upyun.com. 220 Usoppe: King of Snipers Sniper King 500 Command "AUTH" not understood. Name (v2.ftp.upyun.com:chenzhen): XXX/XXX 331 Username ok, send password. Password: 230 Login successful. Remote system type is UNIX. Using binary mode to transfer files. ftp> mkdir 1008 257 "/1008" directory created. ftp> cd 1008 250 "/1008" is the current directory. ftp> put wal* local: wallpaper_1282030786.jpg remote: wallpaper_1282030786.jpg 200 Active data connection established. 125 Data connection already open. Transfer starting. 226 Transfer complete. 43103 bytes sent in 

以上操作便得到了文章前面的图，url为http://chen54.b0.upaiyun.com/1008/wallpaper_1282030786.jpg。 **优点** 1.日志统计流量分析做得超级赞！ 2.操作员和空间的分隔，可能会方便企业用户，不过个人用户可能没什么用，增加了学习成本。 3.各种详细的api以及现成的sdk（但是我没用过）。 **缺点** 1.upyun.com的官网太丑陋了，像是05年前的网站，各种图片像索质量巨差（可能和我用的retina屏有关），第一次打开的时候，我以为是个冒充又拍网的。 2.没有web-ftp有点小遗憾。 **用处** 如果你是一个苦逼创业团队，如果你有需求把apk\ipa包放在哪里，如果你有需求把css/js放在哪里，如果你没有足够多的资金又想有淘宝百度的下载速度，值得一试！

## Comments

**[新一](#15676 "2013-10-11 10:34:16"):** 七牛好像更强大些。而且他有免费的资源可以使用。

**[54chen](#15677 "2013-10-11 13:36:20"):** 七牛好像市场不搞活动。

