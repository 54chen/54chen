title: 改写java TreeMap制造方便查询的ip内存库
link: http://www.54chen.com/java-ee/chang-java-treemap-store-ip.html
author: 54chen
description: 
post_id: 1719
created: 2011/05/30 10:00:21
created_gmt: 2011/05/30 02:00:21
comment_status: open
post_name: chang-java-treemap-store-ip
status: publish
post_type: post

# 改写java TreeMap制造方便查询的ip内存库

![ip](http://img04.taobaocdn.com/imgextra/i4/T1AhydXlJnXXcZ9h76_061839.jpg) 国库流行的ip库记录数大约在6万条左右，数据格式大约都是如下： start,end,country... 要查询的时候都是放到mysql里去，然后select * from ip where start>=xx and end end <=xx来得到ip信息，我（54chen）在搞一个实时监控时，因为并发量非常大，全部查询IP库，需要好几万每秒的查询，特改了java的TreeMap的get方法，将所有数据放到内存里来操作，put的时候还是保持TreeMap的红黑树实现，以start作为key，而在get的时候有一个 compare的过程，在发现compare是大于0的时候进行如下修改： 

> else if (cmp > 0) { int start = ((Data) p.value).getStart(); int end = ((Data) p.value).getEnd(); int intKey = Integer.parseInt(k.toString()); if (intKey >= start && intKey <= end) { return p; } p = p.right; }

大概意思是：在树上遇到比要找的数据小的节点时，从数据中取到end的大小，来参加对比，如果满足要求，直接返回。 附修改好的代码： <http://ishare.iask.sina.com.cn/f/15805095.html> **重要补充：** 由qunar的sunli大侠补充，用实现comparable的key用法更加正宗，代码如下： 

> public class Chen { public static void main(String[] args) { java.util.TreeMap<IpEntry, Data> xmap = new java.util.TreeMap<IpEntry, Data>(); xmap.put(new IpEntry(0, 100), genData(0, 100, "中国")); xmap.put(new IpEntry(101, 200), genData(101, 200, "日本")); xmap.put(new IpEntry(2000, 3000), genData(2000, 3000, "美国")); xmap.put(new IpEntry(201, 300), genData(201, 300, "巴西")); xmap.put(new IpEntry(501, 600), genData(501, 600, "法国")); xmap.put(new IpEntry(701, 800), genData(701, 800, "英国")); xmap.put(new IpEntry(601, 700), genData(601, 700, "北京")); xmap.put(new IpEntry(1601, 1700), genData(1601, 1700, "广州")); Data data = xmap.get(new IpEntry(1260, 1260)); if (data == null) { System.out.println("not found!"); } else { System.out.println(data.getCountry()); } } private static class IpEntry implements Comparable<IpEntry> { final long start; final long end; IpEntry(long start, long end) { this.start = start; this.end = end; } public int compareTo(IpEntry t) { long t1 = start - t.start; if (t1 < 0) { return -1; } long t2 = end - t.end; if (t1 >= 0 && t2 <= 0) { return 0; } return 1; } } private static Data genData(int start, int end, String country) { Data data = new Data(); data.setCountry(country); data.setStart(start); data.setEnd(end); return data; } }

## Comments

**[Fenng](#13580 "2011-05-31 11:28:25"):** 印象中支付宝也有类似的功能，不知道是否做过改写，记得当时对DB的压力可不小呢 :)

**[SamChi](#13576 "2011-05-30 11:38:08"):** 不错！很巧妙的做法～～

