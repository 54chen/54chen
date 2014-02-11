title: 究竟什么是TSRMLS_CC？
link: http://www.54chen.com/php-tech/what-is-tsrmls_cc.html
author: cc0cc
description: 
post_id: 216
created: 2008/12/03 19:21:30
created_gmt: 2008/12/03 11:21:30
comment_status: open
post_name: what-is-tsrmls_cc
status: publish
post_type: post

# 究竟什么是TSRMLS_CC？

> 翻译：[54chen.com]()

原文地址：<http://blog.libssh2.org/index.php?/archives/22-What-the-heck-is-TSRMLS_CC-anyway.html> 如果你曾经做过php内核或者是[php扩展](/c/91)方面的工作，你一定见到过这个到处都是的结构，但是却从来没有人提起过它。知道这是什么的人典型回答不知道的人都会这样说：“不用管它是什么，只需要在这里、这里还有这里用，如果编译器说少一个tsrm_ls，就加一个...”。这样回答并非都是因为这个人懒惰（的确是有一点点懒），其实那只是在说引擎甚至可以不管这个神奇的值在简化什么，这是因为一个新入门的扩展开发者知道它的结构没有什么益处。这信息就像奶牛的意见一样，没关系，就是牛叫而已。 既然我喜欢听自己喋喋不休的无意义主题（这个月我已经少写很多博客了），我想我得弥补这一主题，看看会不会有人通过他能领会什么。你可以责怪Lukas(Lukas Smith 是 PEAR DB 的主要作者应该是这个Lukas，译者注)，他让我一直在[planet-php.net](http://www.planet-php.net/)上。 **术语** TSRM **T**hread **S**afe **R**esource **M**anager - 这是一个经常被忽视的层面，就会有也是很少被讨论到，在你的[PHP源代码](http://www.54chen.com/c/61)包中，它被隐藏在/TSRM目录中。默认情况下，TSRM 层只在编译需要它的SAPI的时候才会打开（例如apache2-worker）。所有的在win32下编译的SAPI都会有TSRM层而不会管它们是否需要。 ZTS **Z**end **T**hread **S**safety -通常情况下，与TSRM有相同的用处。具体来说不同在于，ZTS的使用需要在编译的时候加上参数如./configure ( \--enable-experimental-zts for PHP4, \--enable-maintainer-zts for PHP5），而TSRM是否被使用是由#define的预处理机制来确定的。 tsrm_ls TSRM **l**ocal **s**torage - 当ZTS可用时，这是它在TSRMLS_*宏里传来传去的实际的变量名。它作为一个指向那个线程的独立数据存储块开始位置的指针，我会在一会儿后将它覆盖掉。 TSRMLS_?? 有四个宏设计来为了使ZTS模式和非ZTS模式之间的差异变得平滑，当ZTS不能用时，这四个宏同时都没有任何用处。而在ZTS启用的时候，它们扩大了以下的定义： 
* TSRMLS_C   tsrm_ls
* TSRMLS_D   void ***tsrm_ls
* TSRMLS_CC  , tsrm_ls
* TSRMLS_DC  , void ***tsrm_ls
**综述** 在任何普通的C程序（PHP也一样）中，要在两个不同的函数间传递相同的数据块，你两种办法。 第一种方法是通过函数的参数传递值：

> 
>     #include <stdio.h>
>     
>     void output_func(char *message)
>     {
>         printf("%s\n", message);
>     }
>     
>     int main(int argc, char *argv[])
>     {
>         output_func(argv[0]);
>     
>         return 0;
>     }
    
    
    另一种方法，你可以把这个值保存在一个上层的全局变量中，让函数通过它来取得：

> 
>     #include <stdio.h>
>     
>     char *message;
>     
>     void output_func(void)
>     {
>         printf("%s\n", message);
>     }
>     
>     int main(int argv, char *argv[])
>     {
>         message = argv[0];
>         output_func();
>     
>         return 0;
>     }

这两种方法各有优点和缺点，而且在在实际应用中你常常会看到两者结合使用。事实上，PHP里到处都是全局变量，从类型标识符，到函数回调指针，再到请求特殊的信息例如用于存储用户空间变量的符号表。试图通过函数参数的方法来传递那些变量是不方便的，例如PHP这样的应用常常会需要去注册回调方法来给不支持上下文数据的外部的库使用，此时使用参数传递是不可能的。 因此，共同的信息，像执行栈、函数表、类表以及扩展登记表都是在全局范围里进行声明的，以便它们能在整个应用的任何一个位置都能使用。对于单线程的SAPIs，例如CLI、APACHE1，或者甚至Apache2-prefork，这样做都是完美的。在RINIT/Activation 阶段，请求具体结构被初始化，并在RSHUTDOWN/Deactivation 阶段重新设置回初始的值等待下一次的请求。一个像APACHE1这样特定的WEB服务器，可以一次处理多个页面，是因为它会产生多个进程，每个进程会在自己的进程空间内使用自己的全局变量副本。 现在，让我们来介绍像Apache2-worker或者IIS这样的线程服务器。在这种情况下，在指定时间里只有一个进程空间是可用的，由它抛出多线程处理。每一个线程都会类似一个单独的进程的方式工作。在处理请求的时候服务会一次一个地来。当两个或者多个[线程](/c/71)去处理同一时间的一个请求的时候，麻烦就出现了。每个[线程](http://www.54chen.com/c/73)都试图去使用全局变量来保存它的特定请求信息，并尝试写向同样的存储空间。这样子最少会导致一个脚本里声明的用户空间变量在另一个脚本中出现。而在实践中，这样会导致快速灾难性的错误和不可预测的情况，因为内存可以释放两次或者独立的线程写了相互矛盾的信息。 **不全的全局** 解决方案需要一个引擎、一个核心，还有任何一个使用全局存储的[扩展](http://www.54chen.com/c/91)都要决定会使用多少内存给具体的请求数据。然后，在运行的每一个新的线程中，给它们每个分配一块内存，用来存储它们的数据，从而使每个线程都有自己的本地存储。为了把所有的被指定线程用到的内存块聚成一组，最后一个指针向量被开辟来保存子结构的指针。这个指针指向tsrm_ls变量，被TSRMLS_*家族的宏所使用。为了看清它是怎么工作的，我们先来看一个扩展的例子： 

> 
>     typedef struct _zend_myextension_globals {
>         int foo;
>         char *bar;
>     } zend_myextension_globals;
>     
>     #ifdef ZTS
>     int myextension_globals_id;
>     #else
>     zend_myextension_globals myextension_globals;
>     #endif
>     
>     /* Triggered at the beginning of a thread */
>     static void php_myextension_globals_ctor(zend_myextension_globals *myext_globals TSRMLS_DC)
>     {
>         myext_globals->foo = 0;
>         myext_globals->bar = NULL;
>     }
>     
>     /* Triggered at the end of a thread */
>     static void php_myextension_globals_dtor(zend_myextension_globals *myext_globals TSRMLS_DC)
>     {
>         if (myext_globals->bar) {
>             efree(myext_globals->bar);
>         }
>     }
>     
>     PHP_MINIT_FUNCTION(myextension)
>     {
>     #ifdef ZTS
>         ts_allocate_id(&myextension_globals_id, sizeof(zend_myextension_globals),
>                        php_myextension_globals_ctor, php_myextension_globals_dtor);
>     #else
>         php_myextension_globals_ctor(&myextension_globals TSRMLS_CC);
>     #endif
>     
>         return SUCCESS;
>     }
>     
>     PHP_MSHUTDOWN_FUNCTION(myextension)
>     {
>     #ifndef ZTS
>         php_myextension_globals_dtor(&myextension_globals TSRMLS_CC);
>     #endif
>     
>         return SUCCESS;
>     }

## Comments

**[blankyao](#193 "2009-02-25 15:43:55"):** 还是没搞明白 :(

**[cc0cc](#194 "2009-02-25 22:03:40"):** 其实Sara Golemon讲得还是很明白的，可能是我翻译得不够好，你可以简单地理解为在多线程的webserver里避免线程间的变量冲突的一个东西

**[blankyao](#195 "2009-02-26 08:26:26"):** 我现在就是这样理解的，以后在应用中慢慢理解吧，呵呵

**[air max](#13006 "2010-08-26 15:40:02"):** 还是不太明白哦……楼上的都是专家啊

**[小蔡](#11845 "2009-08-21 02:20:41"):** php_myextension_globals_dtor(zend_myextension_globals *myext_globals TSRMLS_DC) C语言的语法里，在函数参数可以加一个TSRMLS_DC的东西吗？

**[cc0cc](#11846 "2009-08-21 10:00:45"):** 这只是一个宏 TSRMLS_DC , void ***tsrm_ls 如果你这样看 是不是就清楚了呢？ php_myextension_globals_dtor(zend_myextension_globals *myext_globals , void ***tsrm_ls)

**[小蔡](#11847 "2009-08-21 15:17:32"):** 这样理解是比较清楚，但是还是不明白为什么可以这样理解呢？ 呵呵，感觉这个弯总是总是转不过来～ 还有一个问题，看下面两个函数： static int inherit_static_prop(zval **p TSRMLS_DC, int num_args, va_list args, const zend_hash_key *key){...} ZEND_API void zend_do_inheritance(zend_class_entry *ce, zend_class_entry *parent_ce TSRMLS_DC){...} 参数里面的TSRMLS_DC的位置不同会有什么区别呢？如果按上面的理解只是TSRMLS_DC的位置差别而已，是这样的吗？ 谢谢～

**[cc0cc](#11848 "2009-08-21 20:07:12"):** define TSRMLS_DC , void ***tsrm_ls 就是这么一个声明，然后在里面用的时候用了TSRMLS_DC，没有什么特别的难理解吧（注意，是连逗号一起的）？

