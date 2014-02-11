title: [java]如何优雅读取properties文件-part1
link: http://www.54chen.com/java-ee/java-read-properties-files-part.html
author: 54chen
description: 
post_id: 1570
created: 2011/01/18 21:25:22
created_gmt: 2011/01/18 13:25:22
comment_status: open
post_name: java-read-properties-files-part
status: publish
post_type: post

# [java]如何优雅读取properties文件-part1

![java properties](http://www.themoderndaypirates.com/pirates/wp-content/uploads/2010/10/drink_duke_java.jpg) Q:在java中如何加载properties文件或者configure文件才是最好的办法呢？ A:当你在考虑如何加载java的资源文件的时候，许多选择都会立即闪现在你的头脑中：files, classpath resources, 还有URLs。尽管上述所有的方法都能得到最终需要的效果，但经验表明classpath resources 和 URLs 是到目前为止最靠谱的选择。 通常情况下，一个配置文件都有一个异常复杂的结构（比如说xml结构的定义），为了简单，下文里我们以name-value对为例子来讲解（非常类似properties文件的格式）。就算这样，只要你考虑使用inputStream来读取资源文件，你没有理由不采纳下文里提到的办法。 **一、邪恶的java.io.File** 任何没有java背景的人明显的做法是使用原来的files里的足够简单的办法（通过FileInputStream, FileReader,RandomAccessFile）。但是在java应用的布署来说，这是最差的办法。对于追求轻便和不依赖磁盘位置的代码来说，在你的代码中使用绝对文件地址并不是一个很好的方式。使用相对路径看上去是个不错的替代方案，不过不要忘记，是相对于jvm运行时当前的路径。这个相对路径的设置取决于JVM的启动进程，而且会被启动的shell等脚本搞混乱了。如果决定将一些不标准的设置存放依赖最终用户的环境（而且在一些情况下，还未被验证过是否有用户权限），只要换个环境，（比如说EJB或者是WEB应用服务器），你和用户都不能有更多的基于JVM一开始启动时目录的控制。java.io.File是java里最不能跨平台的部分。 java模块的做法是将其加入到classpath中去，直接就可用 。考虑EJB jars，Web应用可以打包成war文件，还有其他类似的便利的布署方式。除非你非得用它，还是对其说不吧。 **二、Classpath resources** 抛开上面所说的坏话，让我们来谈谈更好一点的办法：通过classloaders来加载资源。这样做会更好，因为classloaders本来就扮演了一个资源文件同它的在硬盘上或者其他地方实际位置的关系之间的抽象层。 比如说，你需要从some/pkg/resource.properties加载一个classpath的资源。使用classpath资源是指把文件打包到jar包里或者是在程序运行前加入到classpath里。你可以通过JVM的参数-classpath在每次程序启动前向classpath中写入，也可以一次性写到\classes的位置一直使用。要点是classpath的资源文件布署和java class文件一样，而方便也正是在于此。 从java代码里拿some/pkg/resource.properties有许多方法。首先可以用： 

> `ClassLoader.getResourceAsStream ("some/pkg/resource.properties"); Class.getResourceAsStream ("/some/pkg/resource.properties"); ResourceBundle.getBundle ("some.pkg.resource");`

此外，如果代码在一个位于some.pkg包的class里，下面的代码也可以很好地工作： 

> `Class.getResourceAsStream ("resource.properties"); `

注意两者在参数上微妙的不同之处。所有的getResourceAsStream方法都使用斜杠分割包名的间隔，而且资源文件要包括扩展名。和resource bundles相比，后者更像是java标识符，用点标识包（并且没有文件名后缀）。这是理所当然的，因为resource bundle（资源绑定）可以不仅仅是properties文件，比如还可以是class文件。 稍微有点复杂的地方，java.lang.Class的getResourceAsStream方法的实例方法可以执行相对于包的资源搜索（同样也很灵活，见 http://www.javaworld.com/javaworld/javaqa/2002-11/02-qa-1122-resources.html）。为了区分相对和绝对的资源名字，Class.getResourceAsStream()用斜杠开头表示绝对路径。通常，如果你在代码里不用相对于package的资源的话，没有必要使用这个方法。 ClassLoader.getResourceAsStream(), Class.getResourceAsStream(), ResourceBundle.getBundle()之间微小的区别很容易造成混乱。下面这个表记录了他们之间的特点： **方法操作差异**

Method
Parameter format
Lookup failure behavior
Usage example

ClassLoader.  
getResourceAsStream()
"/"-separated names; no leading "/" (all names are absolute)
Silent (returns null) 
this.getClass().getClassLoader()  
.getResourceAsStream  
("some/pkg/resource.properties")

Class.  
getResourceAsStream()
"/"-separated names; leading "/" indicates absolute names; all other names are relative to the class's package
Silent (returns null) 
this.getClass()  
.getResourceAsStream  
("resource.properties")

ResourceBundle.  
getBundle()
"."-separated names; all names are absolute; .properties suffix is implied 
Throws unchecked  
java.util.  
MissingResource  
Exception
ResourceBundle.getBundle  
("some.pkg.resource")
[未完待续] 54chen copy from http://www.javaworld.com/javaqa/2003-08/01-qa-0808-property.html?page=1

## Comments

**[黑暗浪子](#13345 "2011-01-26 12:58:41"):** 8年前的过时文章现在还在贴？现在读取属性文件根本不这样做，已经out了

**[54chen](#13346 "2011-01-26 13:06:38"):** 觉得讲得透就弄过来了，求比较in的方法。

