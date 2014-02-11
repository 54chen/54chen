title: rose pipe–一次对http技术的伟大革新实现（54chen乱弹版）
link: http://www.54chen.com/architecture/rose-pipe-http-54chen.html
author: 54chen
description: 
post_id: 1270
created: 2010/08/14 18:23:44
created_gmt: 2010/08/14 10:23:44
comment_status: open
post_name: rose-pipe-http-54chen
status: publish
post_type: post

# rose pipe–一次对http技术的伟大革新实现（54chen乱弹版）

![](http://img07.taobaocdn.com/imgextra/i7/T1cjJIXiJxXXaTrOk6_062158.jpg) 网站速度一直是互联网公司所关注的核心目标之一，作为SNS网站更是这样。来自世界第一大的打不开的SNS网站facebook的工程师日志中提到，BigPipe: Pipelining web pages for high performance。 

> 原文在墙外，豆瓣有存根，地址为<http://9.douban.com/site/entry/139173635/>。 来自infoq的一篇资料报道：<http://www.infoq.com/cn/news/2010/08/bigpipe-facebook-optimize> 它本意是充分利用http，将用户感受到的延迟时间降低一半，是如何做到的呢，下面由54chen流水记账一篇解释实现原理。院内曾经由人人网架构师王志亮大侠发表过一篇文章，地址是<http://www.54chen.com/architecture/rose-open-source-portal-framework.html>，本文将以此例中的项目举例。

**HTTP协议** HTTP是一个客户端和服务器端请求和应答的标准，尽管TCP/IP协议是互联网上最流行的应用，HTTP协议并没有规定必须使用它和（基于）它支持的层。 事实上，HTTP可以在任何其他互联网协议上，或者在其他网络上实现。HTTP只假定（其下层协议提供）可靠的传输，任何能够提供这种保证的协议都可以被其使用。 在这里重新解释HTTP是为了后面做铺垫，一次http访问的过程如下： 1.打开一个连接后，客户机把请求消息送到服务器的停留端口上，完成提出请求动作； 2.服务器在处理完客户的请求之后，要向客户机发送响应消息； 3.客户和服务器双方都可以通过关闭套接字来结束TCP/IP对话。 在使用java的ServletResponse的时候，往往都是如下的操作返回结果到用户： 

> out.write(sb.toString()); out.flush(); out.close();

**传统的WEB请求** 以图1为例，一个WEB项目往往由不同的部分组成，不同的格局里往往代表需要从不同的数据表里去取不同的数据。 ![renren page](http://img07.taobaocdn.com/bao/uploaded/i7/T1M_hCXiplXXXZgsbb_123302.jpg) 图1 人人网公共主页页面 一个用户来访问这个页面，按照传统的做法，其流程图可能是如图2这样的。 ![54chen:rose pipe](http://img02.taobaocdn.com/imgextra/i2/T15jFIXbxsXXc0QmZW_024631.jpg) 图2 一个传统的http请求过程 在图2中可以看到，一次打开网站页面的过程中，请求发到后端进行了处理（1和2步），只有当后端的取数据操作（2步）全部完成的时候，才可能进入第3步，向用户返回组装好的html页。如果说图1中一共有四个模块，对应后台有四条sql语句的话，那么，必须这四条sql语句全部返回了结果，才可能让用户看到页面。 **pipe** pipe技术充分利用了前后端技术。将一个页面里的多个模块分成不同的window，多线程取数据的操作，然后再充分利用http请求的连接，将原来的输出，从一次flush变成多次flush： 

> out.write(“基础的dom”); out.flush(); //数据一准备好时 out.write(“js带数据一”); out.flush(); //数据十二准备好时 out.write(“js带数据二”); out.flush(); out.close();

其过程如图3所示： ![54chen:rose pipe](http://img05.taobaocdn.com/imgextra/i5/T13kJIXaNaXXatZfg2_044035.jpg) 图3 pipe的请求过程 借用big pipe的代码，第一次是输出的： `<div> <div id=”left_column”> <div id=”pagelet_navigation”></div> </div> <div id=”middle_column”> <div id=”pagelet_composer”></div> <div id=”pagelet_stream”></div> </div> <div id=”right_column”> <div id=”pagelet_pymk”></div> <div id=”pagelet_ads”></div> <div id=”pagelet_connect”></div> </div> </div>` 当有了完整的dom结构时，浏览器就会开始显示没有数据的框架了。 后面的数据每次都以js继续发送到页面中，浏览器收到即开始写入： ` <script type="text/javascript"> big_pipe.onPageletArrive({id: “pagelet_composer”, content=<HTML>, css=[..], js=[..], …}) </script>` **性能** 这种显示方式的性能，再借用facebook的图来表示之，如图4： ![facebook:pipe](http://sphotos.ak.fbcdn.net/hphotos-ak-snc3/hs539.snc3/30586_396772147199_9445547199_3988332_604391_n.jpg)

## Comments

**[wanglei](#13781 "2011-09-09 18:22:33"):** facebook的bigpipe 应该有多线程和单例两种实现方式吧(不同场合)

**[54chen](#12698 "2010-08-16 17:52:26"):** 本文所提及的是来自rose的新特性，叫做rose pipe，和big pipe是一样的原理，不一样的地方是rose pipe利用了rose portal原有的多线程处理优势，天然地原汗原味地集合在一起了。

**[bll](#12717 "2010-08-18 10:06:47"):** another comet implement?

**[jixilq](#12718 "2010-08-18 12:51:37"):** 请问如果是bigpipe的话，它使用php实现的，没有多线程，那么如何实现服务器的并行化处理呢

**[54chen](#12724 "2010-08-18 19:10:19"):** facebook对php的研究已经不是市面上的php了吧，可能他们的后面已经是c或者c++这些了

**[jixilq](#12732 "2010-08-19 16:12:32"):** 多谢您的回复，最近正在看这方面的东西，比较有兴趣，网上的资料比较少所以只能问问题了

**[chenqj](#12694 "2010-08-16 12:55:07"):** 貌似文中提到的rose portal和facebook的big pipe还是很不一样的，尤其是rose portal说道“并最后统一返回把html输出给浏览器”，这样看来rose portal更像是一个SEDA的实现，pipe主要体现在服务端。而big pipe是结合了服务端、http协议、浏览器端的技术实现出来，pipe体现在服务端、http response，浏览器解析、加载多个方面，确实很牛

**[54chen](#12683 "2010-08-15 13:49:00"):** 你理解错了，这里并不使用ajax，而是充分利用一次http请求中的tcp连接，像管道一样来用它

**[ayanamist](#12681 "2010-08-15 11:06:23"):** 这不是facebook提出来并大规模采用的技术么。和http协议有啥关系？还以为是http1.1协议的改进，原来仅仅是大规模使用ajax而已。

