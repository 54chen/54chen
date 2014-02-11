title: 五四陈透过PHP看JAVA系列:fsockopen
link: http://www.54chen.com/php-tech/54-chen-through-php-to-see-java-series-fsockopen.html
author: cc0cc
description: 
post_id: 997
created: 2010/03/09 16:01:12
created_gmt: 2010/03/09 08:01:12
comment_status: open
post_name: 54-chen-through-php-to-see-java-series-fsockopen
status: publish
post_type: post

# 五四陈透过PHP看JAVA系列:fsockopen

本系列主要总结PHP与JAVA之间的一些异同，给从PHP学JAVA或者是从JAVA学PHP的同学一点启发，五四陈科学院出品。 **第一部分，先看PHP的fsockopen** 先来看PHP手册中的定义<http://www.54chen.com/p/function.fsockopen.html> 看完定义后，来看一段代码，利用PHP去更新squid的缓存： 

> <?php function updateCache($myUrl) { global $vSquidConfig; foreach ( $vSquidConfig ['host'] as $mySquid ) { $myHandle = @fsockopen ( "127.0.0.1", 80, $myErrNo, $myErrStr, 30 ); if ($myHandle) { $myResult = ''; @fputs ( $myHandle, "PURGE " . $myFileName . " HTTP/1.0\r\n" ); @fputs ( $myHandle, "Accept: */*\r\n" ); @fputs ( $myHandle, "\r\n\r\n" ); while ( ! feof ( $myHandle ) ) { $myResult .= fgets ( $myHandle, 128 ); } @fclose ( $myHandle ); } } } ?>

**第二部分，再看JAVA如何达到这个目的**

> public void flush[Squid](/php-tech/%e5%a6%82%e4%bd%95%e7%94%a8php%e4%bb%a3%e7%a0%81%e6%b8%85%e7%a9%basquid%e7%bc%93%e5%ad%98%e4%b8%8b%e7%9a%84%e6%8c%87%e5%ae%9a%e6%96%87%e4%bb%b6.html)() { try { int port = 80; Socket socket = new Socket("127.0.0.1", port); String path = http://test.com/test.html"; BufferedWriter wr = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream(), "UTF-8")); wr.write("PURGE " + path + " HTTP/1.0\r\n"); wr.write("Accept: */*\r\n"); wr.write("\r\n\r\n"); wr.flush(); // Get response BufferedReader rd = new BufferedReader(new InputStreamReader(socket.getInputStream())); String line; while ((line = rd.readLine()) != null) { // Process line... System.out.println(line); } wr.close(); rd.close(); } catch (Exception e) { } }

亲爱的读者，您喜欢用PHP还是JAVA？

## Comments

**[wuei](#12181 "2010-03-09 16:38:45"):** php

**[cc0cc](#12182 "2010-03-09 21:34:34"):** 同好~

**[laruence](#12183 "2010-03-10 10:08:26"):** 这种比较,,能比出来什么.

**[cc0cc](#12184 "2010-03-10 10:51:04"):** 从一种语言转另一种语言的时候，特别都想去用前一种的方式思考问题，找解决方案，这比较适合从PHP转JAVA的同志参考代码

