title: cloud foundry之cloud_controller代码解读
link: http://www.54chen.com/architecture/cloud-foundry-cloud_controller.html
author: 54chen
description: 
post_id: 2210
created: 2012/08/30 17:27:39
created_gmt: 2012/08/30 09:27:39
comment_status: open
post_name: cloud-foundry-cloud_controller
status: publish
post_type: post

# cloud foundry之cloud_controller代码解读

![cloud](http://img.taobaocdn.com/imgextra/i4/13078490/T2UAulXnlbXXXXXXXX_!!13078490.png) **简介** cloudfoundry是vmvare推出来的开源PaaS平台，cloud_controller是其管理界面的http rest api入口。 **补课 rails** Rails基于MVC（模型- 视图- 控制器）设计模式。从视图中的Ajax应用，到控制器中的访问请求和反馈，到封装数据库的模型，Rails 为你提供一个纯Ruby的开发环境。 习惯约定大于配置： model类位置app/models/xxx.rb controller位置app/controllers/xxx_controller.rc help位置app/helpers/xxx_helpers.rb view位置app/views/xxx 对应布局app/views/layouts/xxx.rhtml xxx.rxml **开始cloud_controller** https://github.com/cloudfoundry/cloud_controller 里面有两个项目，一个是cloud controller另一个是health_manager，今天要研究的是前者，下节说后者。 **入口** bin/cloud_controller -> config/boot.rb cloud_controller必需要求ruby1.9以上，因为其中有一个fiber需要ruby支持（传说中的Coroutine）。 如果没有配置 cloud_controller使用nginx，则会启用Thin去启动Rack。（Thin是ruby内置的network server，Rack: a Ruby Webserver Interface） **app** 以vmc push举例： vmc push的关键一请求：post apps 对应app/controllers/apps_controller.rb 

> def create update_app_from_params(app) 检查各种参数并赋值 app.save! (app使用了ActiveRecord::Base，ActiveRecord是ruby用来做orm的东东，具体可以认为save!就是入库了) stage_app(app) 去nats注册：result = client.stage(request, AppConfig[:staging][:max_staging_runtime])

**关键点** cloud_controller接收到请求记录到db:postgresql/sqlite(cloud_controller.yml中定义)。 请求同时发送到nats等进一步处理。 ** vmc push所做的事情** 1．发一个POST到”apps”，创建一个app; (本文只做了这一步的分析) 2．发一个PUT到”apps/:name/application”，上传app; 3．发一个GET到”apps/:name/”，取得app状态，看看是否已经启动； 4．如果没有启动，发一个PUT到”apps/:name/”，使其启动。