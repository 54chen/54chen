title: 用 Lighttpd 作为 MogileFS 存储后端 
link: http://www.54chen.com/life/%e7%94%a8-lighttpd-%e4%bd%9c%e4%b8%ba-mogilefs-%e5%ad%98%e5%82%a8%e5%90%8e%e7%ab%af.html
author: cc0cc
description: 
post_id: 312
created: 2008/12/10 13:12:57
created_gmt: 2008/12/10 05:12:57
comment_status: open
post_name: %e7%94%a8-lighttpd-%e4%bd%9c%e4%b8%ba-mogilefs-%e5%ad%98%e5%82%a8%e5%90%8e%e7%ab%af
status: private
post_type: post

# 用 Lighttpd 作为 MogileFS 存储后端 

1. 创建开发子目录 dev/，下载 Lighttpd 源代码并解压到其下，最新 Lighttpd 稳定版代码为 [lighttpd-1.4.19](http://www.lighttpd.net/download/lighttpd-1.4.19.tar.bz2)： 
    
        $ mkdir dev
    $ cd dev
    $ wget http://www.lighttpd.net/download/lighttpd-1.4.19.tar.bz2
    $ tar xjf lighttpd-1.4.19.tar.bz2
    $ cd lighttpd-1.4.19

  2. 安装必要的开发包： 
    
        $ yinst i ports/bzip2-devel

  3. 编译安装： 
    
        $ ./configure
    $ make
    $ sudo make install

安装完成后 lighttpd 就被安装到了 `/usr/local/sbin/lighttpd` 这个位置上。
  4. 新建 lighttpd 配置文件 `/usr/local/etc/lighttpd.conf` （路径随意，lighttpd 启动时指定正确的配置文件路径即可），内容如下： 
    
        server.document-root = "**/home/y/var/mogdata**"
    server.port = **7500**
    server.username= "nobody"
    server.groupname = "nobody"
    server.modules = ( "mod_webdav", "mod_accesslog" )
    
    # performance settings
    server.max-worker = 8
    server.max-fds = 2048
    server.max-connections = 1024
    # no keep-alive permitted
    server.max-keep-alive-requests=0
    
    # specify possible index files for root directory
    index-file.names = ( "index.html", "index.htm" )
    
    # mimetype mapping
    mimetype.assign = (
        ".html"         =>      "text/html",
        ".htm"          =>      "text/html",
        # default mime type
        ""              =>      "application/octet-stream",
    )
    
    # error log file path
    server.errorlog = "**/tmp/error**"
    
    # access log file path
    accesslog.filename = "**/tmp/access**"
    
    # WebDAV options
    webdav.activate = "enable"
    webdav.is-readonly = "disable"

这里将 WebDAV[?](http://twiki.corp.cnb.yahoo.com:9999/twiki/bin/edit/PlatForm/WebDAV?topicparent=PlatForm.%e7%94%a8Lighttpd%e4%bd%9c%e4%b8%baMogileFS%e5%ad%98%e5%82%a8%e5%90%8e%e7%ab%af) 存储根目录设在了 `/home/y/var/mogdata/` 下，将访问和错误日志文件都放到了 `/tmp/` 目录下。设定 `index-file.names` 是为了让 mogilefsd 访问存储根目录检查服务存活状态时不被拒绝，指定 `mimetype.assign` 则仅仅是为了方便浏览器查看存储根目录中的 index.html 文件，不指定亦可。由于 lighttpd 最大请求长度 `server.max-request-size` 默认值为 2GB，因此不需要像 Nginx 那样特别设定一个较大的请求长度限制。
  5. 建立一个空文件 `/home/y/var/mogdata/index.html` 。
  6. 启动 lighttpd： 
    
        $ sudo /usr/local/sbin/lighttpd -f /usr/local/etc/lighttpd.conf

  

### Lighttpd 作为文件下载服务的优化

当 Lighttpd 需要提供大量并行下载大文件的服务时，大部分时间都会浪费在磁盘 I/O 等待上，此时为了提高响应速度最好把网络传输后端更改为 writev： 
    
    
    server.network-backend = "writev"

Peter, linux-sendfile is better 90% of the cases. Only if you suffer a high io-wait value in vmstat, you might want to try writev. *-sendfile offer zero-copy transfers which is just blazing fast. But as soon as you are disk-io-bound they have to wait too and reducing the wait-time becomes more important. 

来源：http://blog.lighttpd.net/articles/2005/11/11/optimizing-lighty-for-high-concurrent-large-file-downloads