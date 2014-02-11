title: 人人网内部培训：java并发编程原理-synchronize volatile-张洁大侠
link: http://www.54chen.com/java-ee/all-networks-in-house-training-java-concurrent-programming-principles-synchronize-volatile-zhang-jie-heroes.html
author: cc0cc
description: 
post_id: 827
created: 2009/11/11 18:18:53
created_gmt: 2009/11/11 10:18:53
comment_status: open
post_name: all-networks-in-house-training-java-concurrent-programming-principles-synchronize-volatile-zhang-jie-heroes
status: publish
post_type: post

# 人人网内部培训：java并发编程原理-synchronize volatile-张洁大侠

来自人人网内部培训的最新消息： 北京时间今天下午不明时间，一群人在张洁大侠的培训下，对java并发编程原理-synchronize volatile等进行了激励的讨论和学习。出席本次会议的有。。。 言归正传，说，并发，就会出现锁。 **1.互斥锁实现算法** 使用一个并发队列： 

> do{ pre=head; newNode->next=prev; }while(!cas(head,prev,newNode));

此代码中[cas](/34-tcp%e8%bf%9e%e6%8e%a5syn-ack-rst-utg-psh-fin/)是操作系统提供的compare and swap接口，能保证原子性操作（类似memcache的[cas](http://www.54chen.com/814-tokyo-cabinet-with-java-concurrent-test-the-performance-of-a-major-correction-articles/)）。 **2.编译器指令优化使内存[指令](http://www.54chen.com/765-tokyo-cabinet-with-java-concurrent-test-the-performance/)重排序与并发编程** 并发的程序会受到指令重排序的影响，顺序执行的程序逻辑不会受影响。 例子： 

> int a=b=0; Thead1 { a=1; b=2 } Thread2 { if(b==2) { System.out.println(a); //这个a会是多少？ } }

上例中Thread2并不一定会打出1来，因为在Thread1中的两个指令会被编译器优化时交换了顺序，出现了与我们想像中不一样的逻辑。 **3.增加synchronize抑制内存指令重[排序](/784-the-advanced-interconnection-technology-the-use-of-ice-unicom-php-and-java-part-2-client/)** synchronize的作用是什么呢，经常都说是同步，市面上的说法是，jvm在运行的时候，变量会在本地有一个内存区，另外还有一个主存区，同步的含义就是将本地的值同步到主存去。 这个说法没有明确的错误，而且在一定程度上还能帮助你理解synchronize的作用，然而内部真正的原因，是利用synchronize建立内存屏障，使其能够抑制内存指令的重排序，从而使并发程序达到我们想像中的逻辑。 

> int a=b=0; Thead1 { synchronized(this) { a=1; b=2; } } Thread2 { synchronized(this) { if(b==2) { System.out.println(a); //这个a会是多少？ } } }

这样子，代码就不会有逻辑上的问题。 **4.增加volatile建立内存[屏障](/111-plproxy%e9%83%a8%e7%bd%b2%e6%89%8b%e5%86%8c/)** volatile简化了synchronized，原理是一样的。 在增加了volatile后，那个变量所使用的位置就立起了一道 内存屏障，前后的指令顺序都不可打乱。 

> int a=0; volatile  int b=0; Thead1 { a=1; b=2; } Thread2 { if(b==2) { System.out.println(a); //这个a会是多少？ } }

这就可以简化上一个程序。 **5. synchronize的[object](/736-dynamo-based-systems-designed-linkin-voldemort-voldemort-design-chinese-documents-i-am-a-chan-academy-of-sciences-translation-finalized/)（class\this）**

> synchronized(object) { dosomething(); }

这里的object其实是为了使用其上的锁，代码等价如： 

> object.lock.lock(); dosomething(); object.condition.wait(); object.lock.unlock();

【科学院两小时记忆版资料，非原版ppt内容，其中有误的地方定期更正】

## Comments

**[筱白](#11991 "2009-11-12 23:43:20"):** 现在还不看懂java。。。不代表有一天我看不懂。。。。

**[cc0cc](#11992 "2009-11-13 15:30:55"):** 你是还得懂java呢，还是看得懂java....:)

**[石建](#14004 "2011-11-19 19:00:40"):** 1.“增加synchronize抑制内存指令重排序”，为什么“双锁检查”还是存在失败的可能 ？ 2.synchronize是否只能抑制‘源代码’级别的重排，而不能抑制编译后class指令的重排 ？

