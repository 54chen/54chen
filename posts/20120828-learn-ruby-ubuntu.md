title: ruby快速学习手记(ubuntu)
link: http://www.54chen.com/web-ral/learn-ruby-ubuntu.html
author: 54chen
description: 
post_id: 2200
created: 2012/08/28 18:20:13
created_gmt: 2012/08/28 10:20:13
comment_status: open
post_name: learn-ruby-ubuntu
status: publish
post_type: post

# ruby快速学习手记(ubuntu)

![ruby](http://img03.taobaocdn.com/imgextra/i3/T1MQxKXkpmXXcK5RZ8_071520.jpg) ruby 1993年诞生于日本。rails是用ruby写的web framework，所谓的ROR正是这一搭档。 **ruby安装** ubuntu 10.04很简单，安装ruby和rails： sudo apt-get install ruby sudo apt-get install rails **小试牛刀** rails first_rails cd first_rails/ ruby script/server 浏览器打开127.0.0.1:3000 it's works! **IDE:eclipse** **语法特点** 解释型 适合文本处理，类似perl 完全面向对象 无分号 方法定义关键字：def+end 木有大括号（{}） 无需指定类型 局部变量用小写字母或下划线开头即可 全局变量用美元符作为前缀 $；而实例变量用 @ 开头；类变量用 @@ 开头；类名、模块名和常量应该用大写字母开头 常量是以大写字母打头的变量（ruby的常量是可以改变的，但编译器会发出警告。） ruby中这样实例化类：car = Car.new

## Comments

**[bluefrog](#15012 "2012-08-28 19:06:22"):** 上手直接 rails不是很好的哈

