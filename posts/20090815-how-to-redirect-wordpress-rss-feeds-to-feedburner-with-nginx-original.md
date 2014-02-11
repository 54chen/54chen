title: How to: redirect WordPress RSS feeds to feedburner with nginx[原创] 
link: http://www.54chen.com/_linux_/how-to-redirect-wordpress-rss-feeds-to-feedburner-with-nginx-original.html
author: cc0cc
description: 
post_id: 727
created: 2009/08/15 22:01:49
created_gmt: 2009/08/15 14:01:49
comment_status: open
post_name: how-to-redirect-wordpress-rss-feeds-to-feedburner-with-nginx-original
status: publish
post_type: post

# How to: redirect WordPress RSS feeds to feedburner with nginx[原创] 

[转载请注明：<http://www.54chen.com/727-how-to-redirect-wordpress-rss-feeds-to-feedburner-with-nginx-original/> 作者：陈臻] 可能有大多数朋友都会在用wordpress的时候需要从原始的feed地址转到feedburner上，看到一个老外的博客上说了apache的htaccess的修改方案： 

> <IfModule mod_rewrite.c> RewriteEngine on RewriteCond %{HTTP_USER_AGENT} !FeedBurner    [NC] RewriteCond %{HTTP_USER_AGENT} !FeedValidator [NC] RewriteRule ^feed/?([_0-9a-z-]+)?/?$ <http://feeds.feedburner.com/-aboutJavaAndPhp> [R=302,NC,L] </IfModule>

心想这nginx也不能没个着落下，于是就试验了一下，nginx作如下修改即可： 

> if ($http_user_agent !~ FeedBurner) {   rewrite ^/feed$ <http://feeds.feedburner.com/-aboutJavaAndPhp> redirect;   rewrite ^/feed/$ <http://feeds.feedburner.com/-aboutJavaAndPhp> redirect; }

都是302跳，可能对seo不是太好，不过可能是对一些rss客户端比较好。