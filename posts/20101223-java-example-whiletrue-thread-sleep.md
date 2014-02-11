title: [java example]你还在使用while(true) Thread.sleep吗？
link: http://www.54chen.com/java-ee/java-example-whiletrue-thread-sleep.html
author: 54chen
description: 
post_id: 1506
created: 2010/12/23 10:38:55
created_gmt: 2010/12/23 02:38:55
comment_status: open
post_name: java-example-whiletrue-thread-sleep
status: publish
post_type: post

# [java example]你还在使用while(true) Thread.sleep吗？

![java thread](http://img02.taobaocdn.com/imgextra/i2/T1LfNVXl0lXXcCtPs9_103827.jpg) 做各种SERVICE的时候，常常会需要一个程序重复定时地执行，基本上常见的山寨写法都是如下所示： 

> while (true) { System.out.println("yours code"); Thread.sleep(3000); }

但实际这样的写法可控性很低，JDK的java.util.concurrent中提供了大量的方法去控制一段代码定时执行，标准的改写上面的代码如下： 

> ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1); scheduler.scheduleWithFixedDelay(new Runner(), 0, 3, TimeUnit.SECONDS); public class Runner implements Runnable { public void run() { System.out.println("yours code"); } }

scheduler下有很多的方法，达到的目的有：定时执行，执行完后一段时间再次执行；而Executors更有更多控制线程的方法，平时不可忽视。