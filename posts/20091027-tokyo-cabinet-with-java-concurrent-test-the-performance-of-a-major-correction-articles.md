title: 用java并发测试tokyo cabinet的性能[重大更正篇]
link: http://www.54chen.com/_linux_/tokyo-cabinet-with-java-concurrent-test-the-performance-of-a-major-correction-articles.html
author: cc0cc
description: 
post_id: 814
created: 2009/10/27 15:25:31
created_gmt: 2009/10/27 07:25:31
comment_status: open
post_name: tokyo-cabinet-with-java-concurrent-test-the-performance-of-a-major-correction-articles
status: publish
post_type: post

# 用java并发测试tokyo cabinet的性能[重大更正篇]

在前面一篇文章 [用java并发测试tokyo cabinet的性能[五四陈手记]](/765-tokyo-cabinet-with-java-concurrent-test-the-performance/) 提到了测试tc的效率问题，最后的结论是70W/s，由于当时的错误，导致了一些严重影响大家的结论，如今本着认真治学，谨慎小心的态度，重新公布最新的代码和结论，还望受影响的同志们不要发烧。。。 首先，总结上一次为什么会犯错的原因： 1\. 测试代码有问题，TDB db = new TDB();不能放在线程中去new，也许是tc实现的问题，详细原因没有去研究。 2.赶着时间测，把写入的时候的结果直接给屏了。 重新公布新的测试代码： 

> package test; import java.util.ArrayList; import java.util.HashMap; import java.util.List; import java.util.Map; import java.util.Random; import java.util.concurrent.atomic.AtomicLong; import tokyocabinet.*; public class BenchMark {  private static List<TDB> dbList = new ArrayList<TDB>();  public static class Stat {  private AtomicLong _count = new AtomicLong(0);  private static Stat _instance = new Stat();  public static Stat getInstance() {  return _instance;  }  private Stat() {  _printer = new RatePrinter(_count);  _printer.setDaemon(true);  _printer.start();  }  public void inc() {  _count.incrementAndGet();  }  private RatePrinter _printer;  private static class RatePrinter extends Thread {  private long _last;  private AtomicLong _c;  public RatePrinter(AtomicLong c) {  _c = c;  }  @Override  public void run() {  while (true) {  try {  long current = _c.get();  System.out.println("Rate: " + (current - _last) + " req/s");  _last = current;  Thread.sleep(1000L);  } catch (Throwable e) {  e.printStackTrace();  }  }  }  }  }  public static class EchoThread extends Thread {  // private TDB tdb;  public EchoThread(String ip, String port, int in, ThreadGroup group) {  super(group, "EchoThread-" + in);  // // create the object  // TDB tdb = new TDB();  //  // // open the database  // if(!tdb.open("casket"+in+".tct", TDB.OWRITER | TDB.OCREAT)){  // int ecode = tdb.ecode();  // System.err.println("open error: " + tdb.errmsg(ecode));  // }  }  @Override  public void run() {  int index = 0;  // create the object  Random r = new Random();  // open the database // if (!tdb.open("casket" + Thread.currentThread().getId() + ".tct", TDB.OWRITER | TDB.OCREAT)) { // int ecode = tdb.ecode(); // System.err.println("open error: " + tdb.errmsg(ecode)); // }  while (true) {  try {  TDB tdb = dbList.get(0);  String pkey = index + "asdf";  Map<String, String> cols = new HashMap<String, String>();  cols.put("name", "mikio" + index);  cols.put("age", "30");  cols.put("lang", "ja,en,c");  if (!tdb.put(pkey, cols)) {  int ecode = tdb.ecode();  System.err.println("put error: " + tdb.errmsg(ecode) + " key:" + pkey + "  value:" + cols);  }  // client.insert("Table1", "name"+index, "Standard1:name",  // ("name"+index).getBytes("UTF-8"),  // System.currentTimeMillis(), true);  // client.get_column("Table1", "name0", "Standard1:name");  index++;  Stat.getInstance().inc(); } catch (Throwable e) {  e.printStackTrace();  break;  } finally {  // close the database  //if (!tdb.close()) {  //int ecode = tdb.ecode();  // System.err.println("close error: " +  // tdb.errmsg(ecode));  //}  }  }  }  }  /**  * @param args  * @throws TTransportException  */  public static void main(String[] args) {  if (args.length != 1) {  System.out.println("Usage: Benchmark <concurrent>");  System.exit(1);  }  String ip = args[0];  String port = args[0];  Integer concurrent = Integer.valueOf(args[0]);  System.out.println("ip = " + ip + ",port = " + port + ",concurrent = " + concurrent);  ThreadGroup group = new ThreadGroup("Benchmark");  List<Thread> threads = new ArrayList<Thread>();  for (int i = 0; i < concurrent; i++) {  TDB db = new TDB();  //db.optimize();  if (!db.open("./test" + i + ".tdb", TDB.OCREAT | TDB.OWRITER)) {  int ecode = db.ecode();  System.err.println("open error: " + TDB.errmsg(ecode));  continue;  }  dbList.add(db);  }  for (int x = 0; x < concurrent; ++x) {  Thread t = new EchoThread(ip, port, x, group);  threads.add(t);  t.start();  }  } }

对比上一次的代码，能够发现，1.new TDB的过程扔进了Thread.start之前；2.在thread中使用一个全局的变量来获取当前的对象。 启十个进程，全往第一个里写： concurrent = 10 Rate: 25 req/s Rate: 119617 req/s Rate: 130620 req/s Rate: 144202 req/s Rate: 120458 req/s Rate: 112809 req/s Rate: 120800 req/s Rate: 122290 req/s Rate: 119526 req/s Rate: 111189 req/s Rate: 112483 req/s Rate: 109138 req/s Rate: 115648 req/s Rate: 119419 req/s Rate: 105558 req/s Rate: 110230 req/s Rate: 116507 req/s Rate: 105367 req/s Rate: 103781 req/s Rate: 106618 req/s Rate: 107698 req/s Rate: 116768 req/s Rate: 107244 req/s 保持在10w/s的写入速度，到达30s左右以后，数据急转直下： Rate: 48060 req/s Rate: 6901 req/s Rate: 4987 req/s Rate: 46229 req/s Rate: 46686 req/s Rate: 45402 req/s Rate: 6271 req/s Rate: 810 req/s Rate: 33895 req/s Rate: 46548 req/s Rate: 47025 req/s Rate: 6995 req/s Rate: 860 req/s 这，就是tc的table表在写入一个[ArrayList](/765-tokyo-cabinet-with-java-concurrent-test-the-performance/)的真实速度（4核8G）。 官方发言的100W只需要0.4s，说的是写入的hash表，而且数据是纯线性的数字。 提升速度和稳定的办法，和张宴兄弟商量，b+tree类型的数据稳定一些，设置tctdbsetxmsiz也能解决燃眉之急。

## Comments

**[agentzh](#11957 "2009-11-02 10:09:41"):** 没有单机磁盘可以做到“没有写满的一天”，呵呵。

**[agentzh](#11955 "2009-11-02 09:24:34"):** 貌似你在使用 TC 的 hash 的时候，没有调整 bnum 的值？我没记错的话，默认仅 30 万个桶，这意味着一旦超过 30 万数据，则会大量出现 key conflict，性能急转直下，也便不足为奇了，呵呵。另外，还有其他的 TC 优化选项，默认的都很保守 ;)

**[cc0cc](#11956 "2009-11-02 09:57:32"):** 看上去调大这个值 持续写入的话，还是会有写满的一天，那还是不可取的，我都转向了用mysql模拟key-value，毕竟是更熟悉的东西

**[cc0cc](#11961 "2009-11-02 18:01:58"):** 高并发的一直写入下，tc调整大了bnum能抗一阵速度很快，但mysql可以一直抗到硬盘满了，速度一直中等。 ：P

**[agentzh](#11962 "2009-11-03 15:30:56"):** 一般地，bnum 应当设置为总数据量的 2 倍以上，这不同于把 mmap size 调大一些就足够了。在这里便是用完你磁盘的所需的 key 的数量的 2 倍以上。 因为这里咱们本质上使用的是 hash 数据结构，自然要把 bucket 的数目设置得合理一些。呵呵。 另外，10 并发感觉小了些，可以试试更高一些的并发，比如 C10K？呵呵。

**[cc0cc](#11963 "2009-11-03 16:11:20"):** 发现大于10后并发的增加对速度影响不大了 bnum mmap size之间是什么关系呢，我这里用到的是table表，好像性能不错的是b+ ：）

