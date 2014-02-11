title: [五四陈小技巧]全站换域名时利用nginx和javascript做简单友好的换域名跳转通知
link: http://www.54chen.com/architecture/54-chen-skills-the-whole-point-for-domain-names-using-nginx-and-javascript-to-do-a-simple-jump-to-inform-the-friendly-exchange-of-domain-names.html
author: cc0cc
description: 
post_id: 769
created: 2009/09/09 16:41:21
created_gmt: 2009/09/09 08:41:21
comment_status: open
post_name: 54-chen-skills-the-whole-point-for-domain-names-using-nginx-and-javascript-to-do-a-simple-jump-to-inform-the-friendly-exchange-of-domain-names
status: publish
post_type: post

# [五四陈小技巧]全站换域名时利用nginx和javascript做简单友好的换域名跳转通知

在经过上一波的xiaonei.com转renren.com后，昨夜再次迎来新一波的kaixin->renren code的过程，期间有域名要求用新的域名，并且要老域名有跳转有提示，于是采用了下面的办法。 老域名是 old.com 新域名是 new.com 迁移挂维护的过程就略过了，直接说重点， 第一步，修改nginx.conf，删除原来old.com的定义，增加： 

> server { listen 80; server_name old.com; location / { root   /old; if (!-e $request_filename){ rewrite ^/(.*)?$ /index.html?t=$1 last; } } }

第二步，在/old下放进一个index.html，[代码](/576-54%e9%99%88%e7%a7%91%e5%ad%a6%e9%99%a28021x%e6%ba%90%e4%bb%a3%e7%a0%81%e4%b8%8b%e8%bd%bd/)如下： 

> <div class="all-error"> <h1><span>old换名啦！</span> <br> 原来old更名为new，请放心不会影响您的使用，新的地址为<a href="http://new.com" id="link">new.c om</a>谢谢您的支持！ </h1> <p> 2秒之后自动跳转到新的地址... </p> </div> <script type="text/javascript"> document.getElementById("link").href = location.href.replace("old.com","new.com"); setTimeout(function(){ location.href =  location.href.replace("old.com","new.com"); }, 2000) </script>

第三步，关机，回家睡觉