title: [java]如何优雅读取properties文件-part2
link: http://www.54chen.com/java-ee/java-read-properties-files-part2.html
author: 54chen
description: 
post_id: 1594
created: 2011/01/25 16:25:09
created_gmt: 2011/01/25 08:25:09
comment_status: open
post_name: java-read-properties-files-part2
status: publish
post_type: post

# [java]如何优雅读取properties文件-part2

![java properties](http://www.oracle.com/technetwork/java/javaspotlight-189455.png) 接上part1:<http://www.54chen.com/java-ee/java-read-properties-files-part.html> **从数据流到java.util.Properties** 你应该注意到之前提过的方法只是一半的措施：他们都只返回输入数据流，而并没有类似键值对的返回。幸运的是，把数据加载成一个列表很简单（可以实例化java.util.Properties即可）。因为你会发现你在一再地使用它，搞成几个帮助类是有意义的。 java的内置方法给classpath加载指定的资源有小小的不同也是一件讨厌的事情，特别是当一些资源名字是硬编码但你现在想换另一个加载的方法时。抽取出来一些东西是有意义的，类似斜杠和点作为命名的分隔符等等。干脆一点，帖出我的properties的处理类，代码在这里下载：http://www.javaworld.com/javaqa/2003-08/01-qa-0808-property.html?page=2#resources ［代码略］ 在loadProperties方法的javadoc里的注释显示这个方法的输入参数要求非常随意：接受资源名字被任何按照原生的方法设计（除了相关的包外尽量使用Class.getResourceAsStream()）的格式化而且使其本地实现标准化。 短一点的loadProperties() 公用方法决定了哪个类加载器加载资源。下面的解决方法是合理的但并非完美。你应该考虑使用文章"[Find a Way Out of the ClassLoader Maze](http://www.javaworld.com/javaworld/javaqa/2003-06/01-qa-0606-load.html)"里提到的技术来代替。 注意有两个条件编译的常量来控制loadProperties的行为，你可以调整它们来适应你的口味： THROW_ON_LOAD_FAILURE选择loadProperties在找不到资源的情况下是抛异常还是返回空。 LOAD_AS_RESOURCE_BUNDLE 选择资源在查找的时候是绑定资源还是给出的classpath资源。 将LOAD_AS_RESOURCE_BUNDLE设置为true是不好的，除非你是想通过编译到java.util.ResourceBundle的本地化支持得到好处。而且，java内部缓存了资源绑定，所以你可以避免重复地对同样的资源名字进行磁盘文件读写。 **更多的事情** 我有意省略了一个有趣的classpath资源加载方法，ClassLoader.getResources。尽管它不常使用，但其允许许多有用的选项，这些选项在设计高度定制和简单配置的应用程序非常有用。 我没有在这文章里讨论ClassLoader.getResources是因为它值得专门写一篇文章。碰巧，这个方法与剩下的取得资源的方法联系紧密：Java.net.URLs。你可以使用他们，因为资源描述符要比classpath资源名字符要更通用。

## Comments

**[thanq](#13429 "2011-03-17 16:36:08"):** 受用了~ 呵呵

