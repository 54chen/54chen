title: 闲话maven m2eclipse不再支持nested module的原因
link: http://www.54chen.com/java-ee/maven-m2eclipse-not-support-nested-module.html
author: 54chen
description: 
post_id: 1608
created: 2011/02/15 00:02:13
created_gmt: 2011/02/14 16:02:13
comment_status: open
post_name: maven-m2eclipse-not-support-nested-module
status: publish
post_type: post

# 闲话maven m2eclipse不再支持nested module的原因

![maven m2eclipse nested module](http://www.etiri.com.cn/publish/ccw_gif/2006/36/a14_1t1.jpg) Maven是基于项目对象模型(POM)，可以通过一小段描述信息来管理项目的构建，报告和文档的软件项目管理工具. 如果你已经有十次输入同样的Ant targets来编译你的代码、jar或者war、生成javadocs，你一定会自问，是否有一个重复性更少却能同样完成该工作的方法。Maven便提供了这样一种选择，将你的注意力从作业层转移到项目管理层。Maven项目已经能够知道如何构建和捆绑代码，运行测试，生成文档并宿主项目网页. 我严重支持大范围使用maven，但对于内嵌module的支持，我表示怀疑。在使用eclipse进行java开发的时候，要使用maven，m2eclipse插件是必不可少的。之所以在新版本的m2eclipse不再支持nested module的功能了，也许是m2eclipse的人员和我一样有相同的担忧。 **1）项目内嵌导致工程脆弱** maven的目标是松耦合项目与项目之间的联系，任何一个项目不用关心所依赖项目的变化和生命周期，关联的项目不用管是不是在你的eclipse workspace中，还是本地的仓库中还是远程的仓库里。但是一旦有了项目的内嵌，这就变成了紧耦合的项目关系，你必须关心子文件夹里的东西，整个项目变得更加脆弱。 **2）浪费时间** 如果你的项目有无数的内嵌子项目，一旦你修改了其中一个子项目的代码，你不得不全部重新检出、测试、打包，这样的生命周期都必须要花时间来做，不必须的时间浪费在这里了。 **3）鼓励独立的项目反对代码重用** 通常情况下，maven鼓励大家把具有独立功能的模块独立成单独的项目。如果你的项目有一部分代码需要被别的项目重用，你应该做的事情是把这部分代码重构出来并成为独立的项目，然后再在两个项目中都添加依赖。这样子做的好处在于，分隔的关系让你的项目之间变得非常清晰。但如果用了maven的项目内嵌模块的功能，东西南北的项目都紧紧地绑在一起，项目将变得越来越难被其他项目再重新调用。 **4）ant痛苦的历史** 我们都还记得ant脚本时代的一个build的xml：执行缓慢、很难被其他工具使用、很难合并。 **5）m2eclipse的nested module为什么要去掉？** 不是去掉了，m2eclipse将子项目嵌套显示变成了扁平的显示。一个多内嵌模块的项目，只需要使用import as maven projects 即可，在workspace中将以扁平的形式显示。 如果你有什么不同的见解，欢迎探讨。

## Comments

**[54chen](#13364 "2011-02-16 12:23:32"):** 大侠也过来了，我以前用0.99的m2eclipse时还是正常的，不过也只是2级的嵌套，不知道再深的嵌套eclipse会出什么状况。

**[54chen](#13363 "2011-02-16 12:19:37"):** Intellij IDEA没用过，eclipse用顺手了，不过看上去是很不错的。

**[kimi](#13361 "2011-02-15 22:40:01"):** 没有看明白博主所说的“nested module”具体是啥，从名字上我猜测可能是通过 module 元素来聚合很多个 maven 项目，如果是这样我觉得未免因噎废食之嫌。另 Intellij IDEA 对 maven 的支持可以说是非常到位的，博主不妨一试

**[juven](#13362 "2011-02-16 00:48:50"):** eclipse天生对嵌套项目结构支持不好，我认为这是主要的因素

