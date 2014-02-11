title: maven库nexus拖不到jar包小记
link: http://www.54chen.com/java-ee/maven-nexus-jar-note.html
author: 54chen
description: 
post_id: 1990
created: 2012/01/30 21:41:42
created_gmt: 2012/01/30 13:41:42
comment_status: open
post_name: maven-nexus-jar-note
status: publish
post_type: post

# maven库nexus拖不到jar包小记

![maven,nexus](http://maven.apache.org/images/maventxt_logo_200.gif) 使用maven，nexus是个不错的选择。 **最常见的问题：**

> 1.SNAPSHOT拿不到最新的包。 2.明明在maven central库里搜索到的包，却提示Missing。 3.plugin提示Missing。 

**1.SNAPSHOT拿不到最新的包。** 这个问题往往得nexus+hudson造成的。因为daily build导致与日俱增的jar包堆积在一个文件夹下，文件数目过多，导致在取最新的包时超时了。 解决办法： 浏览器打开nexus， login->scheduled tasks->add，添加一个定时清理目录的计划任务即可。最后一个选项“remove if released”需要慎重，平时开发可能不是特别正规的时候可能会snapshot和release都需要。 **2.明明在maven central库里搜索到的包，却提示Missing。** 网上大多数说法都是index失败了，个人经验实际不然(当然不排除你的问题就是index失败)。 当nexus.log里没有任何异常时，请注意以下内容： 在central搜索时，如果只有pom没有jar，那说明这个依赖的项目可能有多个依赖，一定要加上pom，默认是jar。 **3.plugin提示Missing。** 通常原因是：nexus设置的搜索顺序有问题。 login->Public Repositories -> configuration 左边的是正在使用的定义好的仓库组，右边是可以使用的，正确的顺序应该是先在Release找，然后Snapshots，然后3rd party，最后才去外网下（这时nexus才拖下来新的jar/pom）。

## Comments

**[54chen](#15460 "2013-03-29 11:11:19"):** 跨项目开发是不好的实践，拆开项目，ownership更加清晰的维护项目是你正确的选择。

**[sun](#15459 "2013-03-29 10:37:38"):** 你们maven库都是怎么管理的？测试和上线部分？多人开发时，开分支开发后maven install，容易覆盖别人的jar，有没有好的最佳实践啊？

