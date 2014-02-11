title: 配置MogileFS客户端PHP接口说明 
link: http://www.54chen.com/life/%e9%85%8d%e7%bd%aemogilefs%e5%ae%a2%e6%88%b7%e7%ab%afphp%e6%8e%a5%e5%8f%a3%e8%af%b4%e6%98%8e.html
author: cc0cc
description: 
post_id: 316
created: 2008/12/10 13:15:09
created_gmt: 2008/12/10 05:15:09
comment_status: open
post_name: %e9%85%8d%e7%bd%aemogilefs%e5%ae%a2%e6%88%b7%e7%ab%afphp%e6%8e%a5%e5%8f%a3%e8%af%b4%e6%98%8e
status: private
post_type: post

# 配置MogileFS客户端PHP接口说明 

* 安装expat包
    
    
    $ yinst i ports/expat
    $ yinst i ports/expat-devel

  * 下载并编译[neon](http://www.webdav.org/neon/),这里使用的是[neon-0.28.1.tar.gz](http://www.webdav.org/neon/neon-0.28.1.tar.gz)
    
    
    解压neon-0.28.1.tar.gz到neon-0.28.1目录内
    $ cd neon-0.28.1
    $ ./autogen.sh
    $ ./configure --with-expat
    $ sudo gmake install

  * 下载并编辑[MogileFS客户端的PHP扩展](http://www.capoune.net/mogilefs/)
    
    
    $ phpize
    $ ./configure --with-mogilefs
    $ sudo gmake install

  * 通过php扩展接口上传文件和获取指定文件的路径
    
    
    /****
     *   Example using mogilefs procedural API
     *   ****/
    $mg_object = mogilefs_connect('10.62.162.75', 6061, 'myDomain');
    var_dump($mg_object);
    
    $file = '/tmp/example/abc.jpg';
    $fh = fopen($file, 'rb');
    $contents = fread($fh, filesize($file));
    fclose($fh);
    
    mogilefs_put($mg_object,$contents , "abc.jpg", 'my_class');
    $paths = mogilefs_get_paths($mg_object, "abc.jpg");
    var_dump($paths);
    mogilefs_close($mg_object);

上传文件前，先确认trackers已经包含myDomain域和my_class类，可以通过如下方式添加 
    
    
    mogadm --trackers=ds1.platform.yahoo.com:6061 domain add myDomain
    mogadm --trackers=ds1.platform.yahoo.com:6061 class add myDomain my_class