title: 用iptables做本机端口转发
link: http://www.54chen.com/_linux_/%e7%94%a8iptables%e5%81%9a%e6%9c%ac%e6%9c%ba%e7%ab%af%e5%8f%a3%e8%bd%ac%e5%8f%91.html
author: cc0cc
description: 
post_id: 262
created: 2009/01/23 22:52:45
created_gmt: 2009/01/23 14:52:45
comment_status: open
post_name: %e7%94%a8iptables%e5%81%9a%e6%9c%ac%e6%9c%ba%e7%ab%af%e5%8f%a3%e8%bd%ac%e5%8f%91
status: publish
post_type: post

# 用iptables做本机端口转发

代码如下： 
    
    
      iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 8080

估计适当增加其它的参数也可以做不同IP的[端口](/c/381)转发。 如果需要本机也可以访问，则需要[配置](http://www.54chen.com/c/406)OUTPUT链： 
    
    
      iptables -t nat -A OUTPUT -p tcp --dport 80 -j REDIRECT --to-ports 8080

原因： 外网访问需要经过PREROUTING链，但是localhost不经过该链，因此需要用OUTPUT，或者POSTROUTING。 POSTROUTING不行，需要看看。