title: 多nginx单php-fpm的配置方法[from科学院]
link: http://www.54chen.com/php-tech/multi-nginx-configuration-of-single-php-fpm-approach-from-academy-of-sciences.html
author: cc0cc
description: 
post_id: 817
created: 2009/11/02 10:27:27
created_gmt: 2009/11/02 02:27:27
comment_status: open
post_name: multi-nginx-configuration-of-single-php-fpm-approach-from-academy-of-sciences
status: publish
post_type: post

# 多nginx单php-fpm的配置方法[from科学院]

用php fastcgi和nginx搭配的形式是很常见的，在很BT的需求下，可能会需要多个nginx走不同的端口，而进程只用一个php-fpm。 本文所讲的环境比上述一句话还要BT，存在一个[php](/806-academy-of-sciences-in-recent-years-a-good-article-summing-up/)-fpm，而且每个nginx都以一个非超级用户所有来进行运行。 这样就存在了一个BT的情况，php-fpm默认不能以root权限运行，而普通用户来运行的话，对其他用户的目录，php-fpm将没有权限，页面在运行php的时候，会提示404。 于是有以下的解决方案： 找到php的源代码 $src_path/sapi/cgi/fpm/fpm_unix.c 在文件顶部增加一个宏定义：#define I_REALLY_WANT_ROOT_PHP 重新 configure make make install 此时再修改[php-fpm](http://www.54chen.com/688-wordpress-nginx-php-cgi-mysql-memory-in-the-128m-to-install-and-optimize-the-vps-notes/).conf 将user group都修改成root（经过上面的修改php-fpm已经支持root了）。 重启php-fpm. [配置](http://www.54chen.com/640-mediawiki-pgsql/)各nginx.conf： location ~ .*\\.(php|php5)?$ { #fastcgi_pass  unix:/tmp/php-cgi.sock; fastcgi_pass  127.0.0.1:9000; fastcgi_index index.php; include fcgi.conf; } 点到即止，不再多说。。。

## Comments

**[agentzh](#11970 "2009-11-04 17:13:56"):** 突然想说一句半题外话，呵呵： 我们发现，对于 nginx + php farm 的并发性能影响最大的经常是，当 php 发起到 DB 或者 memcached 或者后端 HTTP service 时，当前 php 进程是阻塞的。 因此我们打算试试让 php 进程需要发起二级 IO 请求时，直接由 nginx 的 subrequest 来代理之，这样，php 端的 IO 便可以与 nginx 的性能极好的 event model 融为一体，从而提升单机整体的并发能力。（这对于 Perl/Python/Ruby/Haskell/V8 等其他的以 fastcgi 形式挂在 nginx 后面的 web 应用亦是适用的。） 因此我们需要借用 fastcgi 协议去支持 fastcgi 后端直接发起 nginx subreqest，并传递 subrequest 完成后需要打开的“continuation 对象”，并结果和控制权重新交回指定的 fastcgi 后端入口。

**[cc0cc](#11971 "2009-11-04 17:20:30"):** nginx subreqest? 这应该是个天才的想法，不过，这个本身的开销会不会很大？ 现在常用java搞网络，有什么堵的，一个线程搞定，感觉方便太多了。

**[agentzh](#11972 "2009-11-04 17:33:49"):** 由于都是极简单的 C 和 epoll/kqueue/etc 交互，本身的开销极小，呵呵。事实上，有些 Java 框架已经实现了这样的想法，只不过无法脱离其框架单独使用而已，呵呵。 使用多线程拼并发的一个缺点是每个线程都有自己的 thread，呵呵，因此线程数一大，就得用较小的 stack size，于是程序逻辑会受制于很小的 stack size，否则 10K 并发很容易用尽所有的 RAM，因此函数递归啥的，就得悠着点儿用了，呵呵。我昨儿在用 1 万并发去压 tokyotyrant 服务器时，就不得不将 ttserver 使用的 stack size 调小到 500 KB（用 100KB 的话，ttserver 就直接 segfault 栈溢出了，呵呵），这样让它起 3000 个 pthread 就差不多可以扛 10k 并发了。默认的 MB 级的 stack size 一下就把我的本本上的 RAM 都用得差不多了，哈哈，pthread 都无法成功创建。 详情见这篇著名的 C10K 文章中的讨论： http://www.kegel.com/c10k.html 当然，也有一些哥们抨击 multi-thread 在高并发场合的严重的性能开销，是因为缺乏真正靠谱的 multi-thread 实现所导致的，关于此问题在那篇 c10k 中亦有讨论。。。呵呵。

**[agentzh](#11977 "2009-11-05 10:58:11"):** > 一个缺点是每个线程都有自己的 thread， 呃。。。笔误： s/都有自己的 thread/都有自己的 stack/ :P

**[cc0cc](#11978 "2009-11-05 11:01:05"):** 牛，哈哈，你要出个专著才行

