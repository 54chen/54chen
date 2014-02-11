title: 百度hi通讯协议 抓包分析
link: http://www.54chen.com/web-ral/baidu-hi-ethereal-protocol-analysis.html
author: admin
description: 
post_id: 43
created: 2008/04/01 23:05:14
created_gmt: 2008/04/01 15:05:14
comment_status: open
post_name: baidu-hi-ethereal-protocol-analysis
status: publish
post_type: post

# 百度hi通讯协议 抓包分析

ethereal居然自己就分析了msn messager service... [登录] DNS -> TCP 建立TCP连接后,开始发文. 1.本地向Hi发: 内容可能加密了,没看出什么来 1.1 Hi返回:按照MSN协议,应该是返回一个地址了,不过登录前后的Hi-ip一直都没变,估计不是这样子实现的. 1.2 本地响应给Hi: 乱七八糟 1.3...循环五次左右...大都是乱的... 思考: MSNS [对话]待续