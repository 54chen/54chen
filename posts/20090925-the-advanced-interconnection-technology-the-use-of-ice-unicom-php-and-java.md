title: “先进的互联互通技术”--使用ice联通php和java [part 1 server]
link: http://www.54chen.com/php-tech/the-advanced-interconnection-technology-the-use-of-ice-unicom-php-and-java.html
author: cc0cc
description: 
post_id: 779
created: 2009/09/25 11:25:22
created_gmt: 2009/09/25 03:25:22
comment_status: open
post_name: the-advanced-interconnection-technology-the-use-of-ice-unicom-php-and-java
status: publish
post_type: post

# “先进的互联互通技术”--使用ice联通php和java [part 1 server]

PHP没有中间件，导致了对很多麻烦。比如，对数据库的操作必须要链接数据库，然后做SQL操作。WEB程序直接操作数据库的方法，这样会带来很多人为因素的事故隐患，以及管理上的麻烦，尤其是对大型的网站应用来说。规范的模式应该是，WEB程序传递参数到一个服务程序上，由该服务程序进行判断并最终操作数据库或者其它数据文件，这样做到了明确权限控制，和业务类型集中管理。去年伴随着ICE的推出，[PHP](/611-%e5%8e%9f%e5%88%9bdiscuz-bbs%e7%bc%93%e5%ad%98%e6%95%b4%e4%bd%93%e6%96%b9%e6%a1%88/)终于有了可以使用的中间件。在PHP的发展史上，应该是一个里程碑。 **ICE入门** ICE（Internet Communications Engine 网络通讯引擎），是由Corba原核心成员开发的一个开源中间件，据称其各种性能完全优于Corba。除此之外，我们第一次见到了直接支持PHP的中间件。 ICE的安装，去http://www.zeroc.com/download.html 下载一个Ice针对PHP的安装包，按照说明安装上。 ICE的使用：使用Slice（ICE的一种简单语法脚本），编写一个xx.ice文件。可以使用slice2cpp或slice2java等生成一个cpp或java的ICE框架程序，然后在这个框架程序中进行代码编写。ICE在PHP的应用继承了PHP的一贯传统——简单，只需要在php.ini中加载该xx.ice，在PHP中就可以使用了。 

> ICE在PHP应用中的缺点，无法使用PHP编写Server端，其实这也是PHP的一个缺陷——没有完善的多进程和多线程管理机制。因此，Server端可以采用ICE所支持的其它语言进行编写，比如JAVA/C++/VB/PYTHON等。

**开始传说中的“互联互通” **

> **本例以 Ice 3.2.1为例 下载的位置和参考的文档都在下面** <http://www.zeroc.com/download_3_2_1.html> <http://www.zeroc.com/doc/Ice-3.2.1/manual/Slice.5.10.html#50592>

ICE其实是一个网络框架，入门说明中说了，PHP不能用来做服务端，在这里我们使用java来做服务端，用php来连java，达到传说中的“互联互通”。 我们的服务端用Linux，客户端也用Linux（纯废话）。JAVA环境的搭建不在表述了。 在下载可以看出来，服务器支持yum或者是rpm是多么幸福的一件事情，或者搞一份tar.gz的包下来configure make make install，大概也就是这样。好了这样子就表示安装结束了。。。 

> 安装之后的Ice相关路径: slice2cpp,slice2java在/usr/bin/下 Ice.jar 存储于 /usr/share/java/下 相关的Ice的库存储于/usr/lib下.

第1步，建立[服务器](/736-dynamo-based-systems-designed-linkin-voldemort-voldemort-design-chinese-documents-i-am-a-chan-academy-of-sciences-translation-finalized/)端的demo.ice的文件（注意代码里的半全角，如果是全角自行转成半角）： 

> module Demo{ interface test{ string   execute(string mth,string cmd); }; };

第2步，执行: slice2java demo.ice 生成一堆文件。。。 第3步，动手干活 上一步执行完会在当前目录产生一个Demo目录,目录下自动生成: -rw-r--r--  1 root root 2316  4月 15 17:01 _testDelD.java -rw-r--r--  1 root root  560  4月 15 17:01 _testDel.java -rw-r--r--  1 root root 1929  4月 15 17:01 _testDelM.java -rw-r--r--  1 root root 4177  4月 15 17:01 _testDisp.java -rw-r--r--  1 root root 1070  4月 15 17:01 testHolder.java -rw-r--r--  1 root root  488  4月 15 17:01 test.java -rw-r--r--  1 root root  481  4月 15 17:01 _testOperations.java -rw-r--r--  1 root root  460  4月 15 17:01 _testOperationsNC.java -rw-r--r--  1 root root 5418  4月 15 17:01 testPrxHelper.java -rw-r--r--  1 root root  569  4月 15 17:01 testPrxHolder.java -rw-r--r--  1 root root  567  4月 15 17:01 testPrx.java 到目前为止,demo.ice所以Ice接口部分的定义以及相关依赖都已经自动生成. 我们要实现自己的execute方法,覆盖testPrx.java的同名[方法](/272-%e5%a6%82%e4%bd%95%e5%af%b9%e4%bb%98%e6%97%a0%e8%89%af%e6%87%92%e6%83%b0ops%e7%9a%84%e5%8f%af%e8%80%bb%e7%9a%84%e9%99%90%e6%b5%81%e6%8e%aa%e6%96%bd/): 

> //TestImp.java package Demo; import Ice.Current; public class TestImp extends _testDisp{  public String execute(String mth, String cmd, Current __current) {  // TODO Auto-generated method stub  return mth+cmd;  } }

第4步，建立一个Server服务在10000[端口](/264-%e7%94%a8iptables%e5%81%9a%e4%b8%a4%e6%9c%ba%e4%b9%8b%e9%97%b4%e7%9a%84%e7%ab%af%e5%8f%a3%e8%bd%ac%e5%8f%91/)进行侦听 

> //Server.java package Demo; public class Server {  public static void main(String[] args) {  int status = 0;  Ice.Communicator ic = null;  try {  ic = Ice.Util.initialize(args);  Ice.ObjectAdapter adapter = ic.createObjectAdapterWithEndpoints(  "TestAdapter", "default -p 10000");  Ice.Object object = new TestImp();  adapter.add(object, ic.stringToIdentity("TestAdapter"));  adapter.activate();  ic.waitForShutdown();  } catch (Ice.LocalException e) {  e.printStackTrace();  status = 1;  } catch (Exception e) {  System.err.println(e.getMessage());  status = 1;  }  if (ic != null) {  // Clean up  //  try {  ic.destroy();  } catch (Exception e) {  System.err.println(e.getMessage());  status = 1;  }  }  System.exit(status);  } }

以上大部分代码都是框架生成，你只需要在里面填写TODO。把这个代码拖回来用eclipse打成jar包，注意，需要依赖ice.jar这个包，这个包可以在[官方]()网站下载到。<http://www.zeroc.com/download_3_2_1.html> 生成DemoServer.ajr后传到服务器，运行java -cp ./:Ice.jar:DemoServer.jar Demo.Server 运行成功的条件是Ice.jar和DemoServer.jar都在一个目录里。（Ice.jar从下载地址下载） Server运行之后监听于10000端口，需要修改iptables，允许其他机器可以连接。 编辑 iptables vi /etc/sysconfig/iptables 

> -A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 10000 -j ACCEPT 

**继续深入互联互通** 这一节完成了Server，一个10000端口的Server在等待着我们用PHP来联通中。下一节，我们将讲述：IcePHP环境的搭建，PHP调用Ice如何与JAVA互联互通，以及一个完整的Demo，保存好本节的代码，后面还有用处。[未完待续]

## Comments

**[痴心依然](#11894 "2009-09-25 11:40:12"):** 期待下部

**[cc0cc](#11895 "2009-09-25 11:45:59"):** 依然蝈蝈的速度 依然是那么快

