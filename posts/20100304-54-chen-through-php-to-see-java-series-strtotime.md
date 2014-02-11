title: 五四陈透过PHP看JAVA系列：strtotime
link: http://www.54chen.com/php-tech/54-chen-through-php-to-see-java-series-strtotime.html
author: cc0cc
description: 
post_id: 985
created: 2010/03/04 19:48:20
created_gmt: 2010/03/04 11:48:20
comment_status: open
post_name: 54-chen-through-php-to-see-java-series-strtotime
status: publish
post_type: post

# 五四陈透过PHP看JAVA系列：strtotime

本系列主要总结PHP与JAVA之间的一些异同，给从PHP学JAVA或者是从JAVA学PHP的同学一点启发，五四陈科学院出品。 **第一部分，先看PHP的Date/Tme函数** 详细见：<http://www.54chen.com/p/ref.datetime.html> 今天要说的是其中的strtotime函数 <http://www.54chen.com/p/function.strtotime.html> 函数的功能：接受一个包含美国英语日期格式的字符串并尝试将其解析为 Unix 时间戳（自 January 1 1970 00:00:00 GMT 起的秒数）。 例子： 

> <?php echo strtotime("2010-3-3 3:3:3"); ?> 将得到结果： 1267585383

在PHP应用中，经常在需要记录时间的时候，使用Mysql的int(10)来保存这个 Unix 时间戳，并且时间的比较查找等操作，都用大于小于来进行。 **第二部分，看JAVA对应的方法**

> try { fromUnixTime = **new java.text.SimpleDateFormat("dd/MM/yyyy HH:mm:ss").parse("01/03/2010 08:00:00").getTime();** fromUnixTime /= 1000; } catch (ParseException e1) { e1.printStackTrace(); }

看上去有点麻烦，不过因为java中操作mysql的[jdbc](/java-ee/jdbc-socketread0-locked-in-mind-a-mysql-unknown-bug.html)对类型的要求是非常严格的，所以一般在java项目中，不使用int来代替datetime类型的字段。 **第三部分，提升部分，反过来咋整（从Unix时间戳格式化为datetime）？** 先说PHP： 

> <?php echo date("Y-m-d H:i:s",1267585383); ?>

再说JAVA： 

> String date2 = new java.text.SimpleDateFormat("dd/MM/yyyy HH:mm:ss").format(new Date ((long)1267585383*1000)); System.out.println(date2);

注意java中的强转为[long](/java-ee/java-using-the-bdb-notes-from-berkeley-db-java-api-records.html)不能丢，否则计算后结果不正确。