title: 清华万博-精武门 lamp环境内容回顾
link: http://www.54chen.com/php-tech/tsinghua-huambo-fist-of-fury-review-environmental-elements-lamp.html
author: cc0cc
description: 
post_id: 809
created: 2009/10/24 21:01:28
created_gmt: 2009/10/24 13:01:28
comment_status: open
post_name: tsinghua-huambo-fist-of-fury-review-environmental-elements-lamp
status: publish
post_type: post

# 清华万博-精武门 lamp环境内容回顾

精武门版权所有，复制请标明：<http://www.54chen.com/c/809> 第一部分：Linux基础 因为大多数同学没有任何基础，此课程不得不放慢脚步，但不要担心，所涉及到的东西都会在课程中反复讲解。 1.Linux指令： 1.1 SecureCRT连Linux接服务器后，默认进入自己的用户目录下。--/home/xxxx/ 题外： 目录的概念：.（一个点）当前目录 ..(两个点)上一级目录 <以后不能再分不清楚这两个东西了哈> 相对目录和绝对目录：/home/zzz/相对于/home/来说就是zzz目录，就可以cd /home/后再cd zzz进入到/home/zzz （其中滋味要慢慢体会，只能体会，一定要体会清楚） 1.2 Linux命令行 命令[空格]参数1[空格]参数2[空格]。。。 2.常用命令： cp a b --copy的简写 复制文件的意思，从a复制到b mkdir xxx --make directory的简写，新建文件夹的意思，建立一个叫xxx的文件夹 ls --list的简写，显示当前文件夹下所有文件 精武门版权所有，复制请标明：<http://www.54chen.com/c/809> 第二部分：LAMP环境 2.1 编译程序中的三板斧： configure 检测硬件，配置MakeFile文件 make 编译 make install 安装 2.2 apache编译示例 tar -zxvf 1.tar.gz  --解压源码包 cd httpd-2.2/ --进入解压出来的目录 ./configure --prefix=/home/zz/apache make && make install 2.3 nginx + fast-cgi环境 PHP编译安装： tar zxvf php-5.2.10.tar.gz gzip -cd php-5.2.10-fpm-0.5.11.diff.gz | patch -d php-5.2.10 -p1 cd php-5.2.10 **** ./configure --prefix=/home/zhen.chen/php --with-config-file-path=/home/zhen.chen/php/etc --with-iconv-dir=/usr/local --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-discard-path --enable-safe-mode --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --with-curlwrappers --enable-mbregex --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --enable-mbstring --with-gd --enable-gd-native-ttf --with-openssl --enable-pcntl --enable-sockets --with-ldap --with-ldap-sasl --without-pear make && make install ========================================================================================================= nginx编译安装： tar -zxvf pcre-7.9.tar.gz cd pcre-7.9 ./configure make && make install ==================== cd .. tar zxvf nginx-0.7.61.tar.gz cd nginx-0.7.61 ./configure --user=www --group=www --prefix=/home/xxx/nginx --with-http_stub_status_module --with-http_ssl_module make && make install @@@@@@@@@@@@@@@@@@@@@@@@ 增加www用户： groupadd www useradd -g www www ========================================= nginx+php fast cgi配置 1\. cp /home/zhen.chen/fcgi.conf /home/zzz/nginx/conf/ 2\. vi /home/zzzz/nginx/conf/nginx.conf 3\.  location ~ .*\\.(php|php5)?$     {       #fastcgi_pass  unix:/tmp/php-cgi.sock;       fastcgi_pass  127.0.0.1:9000;       fastcgi_index index.php;       include fcgi.conf;     } 4\. sudo /home/zzz/nginx/sbin/nginx 精武门版权所有，复制请标明：<http://www.54chen.com/c/809>

## Comments

**[darling](#11946 "2009-10-24 21:59:43"):** 坚持走PHP主意发展道路！

**[guyu](#11947 "2009-10-24 23:58:09"):** 偶尔过来看见一篇流水账，还是去看看美女那个工具吧

**[郝晋](#11948 "2009-10-25 18:18:59"):** 支持！！！！

**[cc0cc](#11950 "2009-10-26 21:53:19"):** 好好学，机会大大的。。。

**[cc0cc](#11951 "2009-10-26 21:55:36"):** 你应该常来嘛，不过美女那个坏掉了。。。

**[cc0cc](#11952 "2009-10-26 21:57:24"):** 好好学，期待将来过来谈经论道。。。

**[fanglor](#12502 "2010-07-11 09:07:32"):** 以前听说你是做PHP培训的，还不信，这次看来是真的。看一下你友情连接上在有 PHP兄弟连，这课程上面又能有清华万博，有没有机会到我们 北京星模实训学校 来客串一回吧。 我们的网址是 http://www.xingmo.com

**[54chen](#12504 "2010-07-12 08:57:10"):** 我只是业余时间喜欢与人分享技术，而非专业做PHP培训的 听过你们学校哦，以前是不是在清景园那边？ 可以加我QQ 65157616

