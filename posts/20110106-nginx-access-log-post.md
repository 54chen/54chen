title: [nginx]如何在access log中记录post请求的参数
link: http://www.54chen.com/web-ral/nginx-access-log-post.html
author: 54chen
description: 
post_id: 1552
created: 2011/01/06 23:46:09
created_gmt: 2011/01/06 15:46:09
comment_status: open
post_name: nginx-access-log-post
status: publish
post_type: post

# [nginx]如何在access log中记录post请求的参数

![nginx log post data](http://wiki.nginx.org/local/nginx-logo.png) 移动互联网行业开发过程中，服务端经常会需要检查是否收到请求，收到什么样的请求，最简单的办法就是看nginx的access log，常见的nginx配置中access log一般都只有GET请求的参数，而POST请求的参数却不行。 <http://wiki.nginx.org/NginxHttpCoreModule#.24request_body>

> $request_body This variable(0.7.58+) contains the body of the request. The significance of this variable appears in locations with directives proxy_pass or fastcgi_pass. 

正如上文件所示，只需要使用$request_body即可打出post的数据，在现存的server段加上下面的设置即可： 

> ` log_format access '$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent $request_body "$http_referer" "$http_user_agent" $http_x_forwarded_for'; access_log logs/test.access.log access;`

## Comments

**[xiaohuo](#15561 "2013-05-31 00:49:53"):** nginx有办法将request_body的内容以K-V形式中的value传给后端服务器么？我想用rose接收xml格式的请求，您觉得还有别的什么办法么？

**[54chen](#15562 "2013-05-31 10:00:51"):** 按http post的标准做法，body里的内容就是key=v&key2=v2&key3=v3。 python框架喜欢传入json，建议还是使用标准做法。 rose接收xml格式请求你可以做一个parameterResolver来接收String的xml数据之后解成java bean即可。

**[xiaohuo](#15563 "2013-05-31 13:20:10"):** 其实是这样的，最近在看微信的开放平台，微信的请求数据就是post一个xml数据过来，xml数据还是放在httpd协议的content里的，然后发现servlet接口里貌似也没有获得content的方法，也不能通过getParameter因为他就不是kV，貌似只能手动读流，那就和自己手动写一个server没啥区别了。所以您说用parameterResolver来接收String的xml是如何做到呢？其实也就想得到一个string的xml就心满意足了。

**[54chen](#15564 "2013-05-31 14:43:17"):** 那只能手动读流了，没有办法。resolver也需要有一个起码的key

**[54chen](#15030 "2012-09-14 18:47:15"):** 检查一下是不是nginx版本或者配置的问题。

**[testaa](#15028 "2012-09-13 17:58:07"):** 你好我想问下，我用curl 模拟post 随便 post一个值上去。 但是日志里面没有记录到。这个是什么原因。

**[传业](#15110 "2012-11-01 14:24:29"):** 如果有通过post上传文件的话会把文件内容打出来么？

**[54chen](#15111 "2012-11-02 12:05:40"):** 你可以试一试。

**[54chen](#13303 "2011-01-07 13:58:41"):** 呵呵，主要是在调试时使用。

**[tt](#13301 "2011-01-06 23:54:46"):** 不错，不过log文件估计要爆炸了

