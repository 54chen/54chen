title: java List转byte[]的方法
link: http://www.54chen.com/java-ee/java-list-transferred-byte-method.html
author: cc0cc
description: 
post_id: 859
created: 2009/12/08 11:46:33
created_gmt: 2009/12/08 03:46:33
comment_status: open
post_name: java-list-transferred-byte-method
status: publish
post_type: post

# java List转byte[]的方法

先做一个试验： 

> Test.java package test; import java.util.ArrayList; import java.util.List; public class Test { public static void main(String[] args) { List<Integer> lst = new ArrayList<Integer>(); for (int i = 0; i < 100; i++) { lst.add(i); } System.out.print(lst.toString()); } }

得到结果：列出了所有的数字 

> [0, 1, 2, 3, 4, 5, 6。。。

进一步进行改进： 

> package test; import java.util.ArrayList; import java.util.List; public class Test { public static void main(String[] args) { List<BlockData> lst = new ArrayList<BlockData>(); for (int i = 0; i < 100; i++) { lst.add(new BlockData(i,i)); } System.out.print(lst.toString()); } /** * 定义一个乱七八糟的class * @author cc * */ public static class BlockData { public int version; public int count; public List<String> list; public BlockData(int version, int count) { this.version = version; this.count = count; this.list = new ArrayList<String>(count); } public BlockData(int version, int count, List<String> list) { this.version = version; this.count = count; this.list = list; } } }

运行后的结果： 

> [test.Test$BlockData@1bc4459, test.Test$BlockData@12b6651, ...

从后面一个例子看到的object在[toString](/705-peer-to-peer-distributed-hash-algorithm-in-the-mainstream-of-comparative-analysis-of-the-collection/)的时候，对象只是一个串了，要想再恢复成[object](http://www.54chen.com/814-tokyo-cabinet-with-java-concurrent-test-the-performance-of-a-major-correction-articles/)不可能了。54chen曾想把list.toString.getBytes，被证明是不靠谱的做法。 而要想把一个list变成byte[]在网络上传输，看下面的代码： 

> public void encode(OutputStream os) throws IOException { List<NuclearStorageValue> list = data.list; if (list != null && list.size() > 0) { for (NuclearStorageValue value : list) { // 依次写入每条数据的长度和数据内容 byte[] bytes = value.toByteArray(); os.write(int2bytes(bytes.length)); os.write(bytes); } } } public byte[] int2bytes(int val) { byte[] b = new byte[4]; b[0] = (byte) (val >> 24); b[1] = (byte) ((val << 8) >> 24); b[2] = (byte) ((val << 16) >> 24); b[3] = (byte) ((val << 24) >> 24); return b; }

## Comments

**[古道西风](#12058 "2009-12-16 09:15:11"):** 汗啊！！！！！！！！！！！

