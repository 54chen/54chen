title: cloud foundry之vmc代码解读
link: http://www.54chen.com/architecture/cloud-foundry-vmc.html
author: 54chen
description: 
post_id: 2204
created: 2012/08/29 12:11:11
created_gmt: 2012/08/29 04:11:11
comment_status: open
post_name: cloud-foundry-vmc
status: publish
post_type: post

# cloud foundry之vmc代码解读

![cloud](http://img.taobaocdn.com/imgextra/i4/13078490/T2UAulXnlbXXXXXXXX_!!13078490.png) **简介** cloudfoundry是vmvare推出来的开源PaaS平台，vmc全称是The VMware Cloud CLI，是vmware的应用平台命令行接口。 ubuntu安装vmc 

> sudo gem install vmc

**常见的用法 **

> vmc target api.paas.n.xiaomi.net vmc add-user vmc login vmc push 

vmc其实是使用ruby来实现的一整套的cli命令转化为http restful请求的对应工具。 其代码位于：https://github.com/cloudfoundry/vmc git clone回来代码之后，下面对代码进行半瓶水解析法解析。 **入口 ** bin/vmc 引用了 $github/vmc/lib/cli 直接运行VMC::Cli::Runner.run 位于：lib/cli/runner.rb 看上去run会被执行。 **进入到parse_command!方法中** 以push为例： when 'push' usage('vmc push [appname] [--path PATH] [--url URL] [--instances N] [--mem] [--runtime RUNTIME] [--no-start]') if @args.size == 1 set_cmd(:apps, :push, 1) else set_cmd(:apps, :push, 0) end **进入到set_cmd中设置变量** module Cli :Runner module Command :Apps **然后** cmd = VMC::Cli::Command.const_get(@namespace.to_s.capitalize) cmd.new(@options).send(@action, *@args.collect(&:dup)) **最终**

> VMC::Cli::Command::Apps.new().send(push) （在ruby语法中，此处send方法是一个关键词，表示callMethod)

** 于是来到了lib/cli/commands/apps.rb**

> def push(appname=nil) def do_push(appname=nil) upload_app_bits(appname, @application) def upload_app_bits(appname, path) client.upload_app(appname, file, appcloud_resources) lib/vmc/clinet.rb def upload_app http_post(path(VMC::APPS_PATH, name, "application"), upload_data) def http_post(path, body, content_type=nil) def request(method, path, content_type = nil, payload = nil, headers = {}) perform_http_request RestClient::Request.execute(req) 

于是就没有了（RestClient是ruby中一个rest库）。