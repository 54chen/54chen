title: Firefox crawler
link: http://www.54chen.com/life/firefox-crawler.html
author: cc0cc
description: 
post_id: 286
created: 2008/12/10 11:01:31
created_gmt: 2008/12/10 03:01:31
comment_status: open
post_name: firefox-crawler
status: private
post_type: post

# Firefox crawler

* ff crawler 
    * 编译环境
    
    
     
      apt-get build-dep firefox
      sudo apt-get install libdbus-glib-1-dev
       cd mozilla_dir
       emacs .mozconfig
       make -f client.mk build

这个步骤很容易出问题，请参考官方文档 <http://developer.mozilla.org/en/Build_Documentation> ，目前，已经能在ff3.0.1上面成功运行。 

  *     * 代码准备
    
    
       svn co http://10.62.164.60/svn/hack/yaclay_sock/
       cp -r yaclay_sock mozilla_dir
       cd yaclay_sock
       make
    
    
      可执行文件在 mozilla_dir/dist/bin/yaclay

  *     * 目的
    
    
       对付使用javascript来动态生成内容的网站

  *     * 使用方法
    
    
       命令行 ./yaclay [port] [profile]
       默认 [port] 22222 [profile] yaclay
       yaclay作为服务器监听[port]端口,打开名为[profile]的配置文件
       如果某个firefox的进程已经打开了配置文件,启动将会失败,因为配置文件会被上锁

  *     * 客户端行为
    
    
       命令行 telnet 127.0.0.1 [port default=22222]
       连接上之后会收到"200 hello", 之后yaclay会等待客户端命令. 命令以换行为结束.

  *     *       * 命令格式一: loaduri [URL]
    
    
        加载[URL]. 加载结束,yaclay会输出HTML,和"!!!END!!!"

  *     *       * 命令格式二: js [javascript]
    
    
        对[javascript]进行求值,并输出运行结果.通常是最后一个语句的值
        目前js的错误信息只显示在yaclay的中断输出中

  *     *       *         * 例如,显示页面中所有的URL
    
    
         js var ret = ""; var a = document.getElementsByTagName('A'); for(var i=0; i<a.length; i++  ) { ret = ret + "\n" + a[i].getAttribute("href") } ret = ret + "\n!!!END!!!\n"

  *     *       * 命令格式三: echo [args]
    
    
        输出[args],用于检查连接时候中断
       如果连接断开,yaclay将回收这个线程占用的资源

  *     * 编译过程 
      * 下载firefox 3b4 
        * <http://developer.mozilla.org/en/docs/Mozilla_Source_Code_Via_CVS>
    
    
       make -f client.mk build
       进入yaclay_sock目录,执行make
       程序位于dist/bin,需要设置LD_LIBRARY_PATH和MOZILLA_FIVE_HOME

  *     * 修改的过程 
      * 策略
    
    
        尽量不改变已有代码,如果确实改变,建立新的目录
        尽量使用低层的API对应简单的事件管理

  *     *       * 使用winEmbed的代码
    
    
        因为初期使用VS来调试,后来发现GTK太麻烦
       建立了一个虚拟的webnavigator对象
       创建一个socket server (使用necko)

  *     *       * 修改location.setURI的行为
    
    
        用来阻止循环加载
       禁用setTimeout setInterval
       block image refresh

  *     * 痛苦的地方 
      * mozilla代码很多,需要很长入门时间
    
    
        snap:///~/work/mozilla.tree#1:-*- Mode: tree-editor; truncate-lines : t -*-

  *     *       * 需要了解的地方 
        * nspr
    
    
        跨平台函数库:事件,文件...

  *     *       *         * necko
    
    
        网络函数库

  *     *       *         * xpcom
    
    
        xpcom是mozilla中组件的实现

  *     *       *         * nsCOMPtr
    
    
        <http://developer.mozilla.org/en/docs/Using_nsCOMPtr>
        智能指针.负责对象的计数和queryinterface.

  *     *       * nsCOMPtr的麻烦
    
    
       if ref is messed up, core dumps. Or memory leaks.
       xpcom要求对代码结构和linker十分熟悉

  *     *       * debug的痛苦
    
    
       gdb需要消耗极大的内存,一般先把set auto-solib-add 0,需要symbol时再加载
       多线程和网络调试很困难

  *     * 评估
![Image:Yaclay-nohistory.log.jpg](http://10.62.164.49/wiki/images/7/76/Yaclay-nohistory.log.jpg)

取自"<http://10.62.164.49/wiki/index.php/Firefox_crawler>"