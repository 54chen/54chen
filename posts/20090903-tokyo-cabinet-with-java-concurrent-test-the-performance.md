title: 用java并发测试tokyo cabinet的性能[五四陈手记]
link: http://www.54chen.com/java-ee/tokyo-cabinet-with-java-concurrent-test-the-performance.html
author: cc0cc
description: 
post_id: 765
created: 2009/09/03 19:34:24
created_gmt: 2009/09/03 11:34:24
comment_status: open
post_name: tokyo-cabinet-with-java-concurrent-test-the-performance
status: publish
post_type: post

# 用java并发测试tokyo cabinet的性能[五四陈手记]

Tokyo Cabinet 是日本人 平林幹雄 开发的一款 DBM 数据库，该数据库读写非常快，哈希模式写入100万条数据只需0.643秒，读取100万条数据只需0.773秒，是 Berkeley DB 等 DBM 的几倍。 编译安装tokyocabinet数据库 

wget <http://tokyocabinet.sourceforge.net/tokyocabinet-1.4.28.tar.gz> tar zxvf tokyocabinet-1.4.28.tar.gz cd tokyocabinet-1.4.28/ ./configure make make install cd ../

http://tokyocabinet.sourceforge.net/javapkg/tokyocabinet-java-1.22.tar.gz 下载这个包 

tar zxvf tokyocabinet-java-1.22.tar.gz cd tokyocabinet-java-1.22 ./configure make make install

install会将libjtokyocabinet.so 和 tokyocabinet.jar放到/usr/lib64下面。 将生成的tokyocabinet.jar拖到本地，新建项目，引用这个jar包。使用如下代码： 

package test; import java.util.ArrayList; import java.util.HashMap; import java.util.List; import java.util.Map; import java.util.concurrent.atomic.AtomicLong; import tokyocabinet.*; public class BenchMark { public static class Stat { private AtomicLong _count = new AtomicLong(0); private static Stat _instance = new Stat(); public static Stat getInstance() { return _instance; } private Stat() { _printer = new RatePrinter(_count); _printer.setDaemon(true); _printer.start(); } public void inc() { _count.incrementAndGet(); } private RatePrinter _printer; private static class RatePrinter extends Thread { private long _last; private AtomicLong _c; public RatePrinter(AtomicLong c) { _c = c; } @Override public void run() { while (true) { try { long current = _c.get(); System.out.println("Rate: " + (current - _last) \+ " req/s"); _last = current; Thread.sleep(1000L); } catch (Throwable e) { e.printStackTrace(); } } } } } public static class EchoThread extends Thread { private TDB tdb; public EchoThread(String ip,String port, int in, ThreadGroup group){ super(group, "EchoThread-" + in); // create the object tdb = new TDB(); // open the database if(!tdb.open("casket.tct", TDB.OWRITER | TDB.OCREAT)){ int ecode = tdb.ecode(); System.err.println("open error: " + tdb.errmsg(ecode)); } } @Override public void run() { int index = 0; while (true) { try { String pkey = new Long(tdb.genuid()).toString(); Map cols = new HashMap(); cols.put("name", "mikio"+index); cols.put("age", "30"); cols.put("lang", "ja,en,c"); if(!tdb.put(pkey, cols)){ int ecode = tdb.ecode(); //System.err.println("put error: " + tdb.errmsg(ecode)); } //client.insert("Table1", "name"+index, "Standard1:name", ("name"+index).getBytes("UTF-8"), System.currentTimeMillis(), true); //client.get_column("Table1", "name0", "Standard1:name"); index ++; Stat.getInstance().inc(); } catch (Throwable e) { e.printStackTrace(); break; } finally { // close the database if(!tdb.close()){ int ecode = tdb.ecode(); //System.err.println("close error: " + tdb.errmsg(ecode)); } } } } } /** * @param args * @throws TTransportException */ public static void main(String[] args){ if (args.length != 1) { System.out .println("Usage: Benchmark "); System.exit(1); } String ip = args[0]; String port = args[0]; Integer concurrent = Integer.valueOf(args[0]); System.out.println("ip = "+ip+",port = "+port+",concurrent = " + concurrent); ThreadGroup group = new ThreadGroup("Benchmark"); List threads = new ArrayList (); for (int x = 0; x < concurrent; ++x) { Thread t =new EchoThread(ip,port, x, group); threads.add(t); t.start(); } } }

运行结果反馈： 

并发 随机结果 起始文件大小 运行中机器情况

1
Rate: 255153 req/s
517k
load average: 0.53, 0.39, 1.30

5
Rate: 505795 req/s - Rate: 909310 req/s
517k
load average: 3.89, 1.93, 1.65 Mem: 8195328k total, 8098204k used, 97124k free, 6685284k buffersSwap: 4000144k total, 148k used, 3999996k free, 36852k cached

10
Rate: 658089 req/s - Rate: 907266 req/s
517k
load average: 5.34, 2.30, 1.81 Mem: 8195328k total, 8112984k used, 82344k free, 6703212k buffersSwap: 4000144k total, 148k used, 3999996k free, 36800k cached

20
Rate: 694200 req/s - Rate: 1003093 req/s
517k
load average: 9.40, 6.71, 3.62 Mem: 8195328k total, 7423824k used, 771504k free, 6684268k buffersSwap: 4000144k total, 148k used, 3999996k free, 36784k cached
[官方数据](http://tokyocabinet.sourceforge.net/benchmark.pdf)

  * 70W req/s 基本上是一个和谐的值了

## Comments

**[Willko](#11861 "2009-09-03 23:26:11"):** 配置是什么啊？

**[cc0cc](#11863 "2009-09-04 11:59:55"):** 4核8G的一个老机器

**[bayard2009](#11940 "2009-10-14 18:34:48"):** 1、此用例能否在win平台跑起来，如果以后的平台是win如何做？ 2、Tokyo Cabinet 在数据检索和持久化支持方面是否有类似berkely db je的 Second Index 和DPL等API？

**[cc0cc](#11941 "2009-10-14 18:38:35"):** http://tokyocabinet.sourceforge.net 只有linux的，没研究过win的，技术细节你可以看看它的官方文档

