title: cloud foundry之router代码解读
link: http://www.54chen.com/architecture/cloud-foundry-router.html
author: 54chen
description: 
post_id: 2213
created: 2012/09/06 17:15:24
created_gmt: 2012/09/06 09:15:24
comment_status: open
post_name: cloud-foundry-router
status: publish
post_type: post

# cloud foundry之router代码解读

![cloudfoundry](http://img.taobaocdn.com/imgextra/i4/13078490/T2UAulXnlbXXXXXXXX_!!13078490.png) **简介** cloudfoundry是vmvare推出来的开源PaaS平台，router是其所有请求的入口路由，其与nginx进行配合完成各种情况情况下的转发。 代码位置在：https://github.com/cloudfoundry/router **历史** 第一版的router直接通过unix sock将http request通过nginx的proxy 发到了router.rb中处理，第二版的时候，因为ruby直接处理请求性能不行，换成了lua脚本来接收请求，再由ruby程序将结果返回。 **目录结构** router代码不多，共有下面的一些主要目录。 . ├── bin │ └── router （启用router server的脚本） ├── config │ ├── router2.yml │ └── router.yml （配置文件，指定了服务的端口和消息队列pid等） ├── ext │ └── nginx │ ├── tablesave.lua │ └── uls.lua （这两个lua脚本供nginx conf使用，实现用户第一次访问时生成一个cookies，lua让其去同一个地方） ├── Gemfile ├── Gemfile.lock ├── lib │ ├── router │ │ ├── const.rb │ │ ├── router.rb │ │ └── router_uls_server.rb （基于一个简单的web框架(Sinatra)开发的一些http api） │ └── router.rb（负责整体启动router的http server） ├── LICENSE ├── NOTICE ├── Rakefile ├── README └── spec ├── functional │ ├── router_spec.rb │ └── spec_helper.rb ├── integration │ ├── router_spec.rb │ └── spec_helper.rb ├── lib │ ├── nats_timeout.rb │ └── spec_helper.rb ├── Rakefile ├── spec_helper.rb └── unit └── router_spec.rb **nginx的关键配置** nginx对接的配置在https://github.com/cloudfoundry/vcap/blob/master/dev_setup/cookbooks/nginx/templates/default/router-nginx.conf.erb，是动态生成的，在安装时决定。 其中，关键的内容是： #定义lua运行的环境 lua_package_path "<%= node[:lua][:module_path] %>/?.lua;;"; lua_package_cpath "<%= node[:lua][:module_path] %>/?.so;;"; #upstream定义到nls服务上 upstream router_status { server <%= node[:nginx][:uls_ip] %>:<%= node[:nginx][:uls_port] %>; } 其中，根据 health_monitor的值穿插了subrequest去query /vcapuls的subrequest，而 /vcapuls是接到router.sock的。 简单地说，第二版的变化在于，大量的业务请求在health_moniter确定之后，变成了nginx直连下属业务，而不再经过router了。 **学到什么** 1.nginx_lua相当好用，还得感谢之前的老同事agentzh他们的贡献; 2.router用http这么来回的搞感觉不如tcp来得靠谱。