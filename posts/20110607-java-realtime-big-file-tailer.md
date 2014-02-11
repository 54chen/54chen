title: java实时日志大文件分析
link: http://www.54chen.com/java-ee/java-realtime-big-file-tailer.html
author: 54chen
description: 
post_id: 1710
created: 2011/06/07 08:16:04
created_gmt: 2011/06/07 00:16:04
comment_status: open
post_name: java-realtime-big-file-tailer
status: publish
post_type: post

# java实时日志大文件分析

![java logger tailer](http://img04.taobaocdn.com/imgextra/i4/T14z9eXa4fXXXUxOU4_053357.jpg) 日志分析时经常会遇到这个问题：一个正在增长中的大文件，要分析新增的每一行的固定内容进行统计并展示啥的。用java的RandomAccessFile盯住文件的末尾，是一个不错的选择，下面的实现，是一个高效的java tail工具，实现类似linux的tail工具的功能。用来做大日志文件的实时分析，是一个不错的选择。 

> public class LogFileTailer extends Thread { private Logger logger = LoggerFactory.getLogger(LogFileTailer.class); /** * How frequently to check for file changes; defaults to 5 seconds */ private long sampleInterval = 5000; /** * The log file to tail */ private File logfile; /** * Defines whether the log file tailer should include the entire contents of * the exising log file or tail from the end of the file when the tailer * starts */ private boolean startAtBeginning = false; /** * Is the tailer currently tailing? */ private boolean tailing = false; /** * Set of listeners */ private Set listeners = new HashSet(); /** * Creates a new log file tailer that tails an existing file and checks the * file for updates every 5000ms */ public LogFileTailer(File file) { this.logfile = file; } /** * Creates a new log file tailer * * @param file The file to tail * @param sampleInterval How often to check for updates to the log file * (default = 5000ms) * @param startAtBeginning Should the tailer simply tail or should it * process the entire file and continue tailing (true) or simply * start tailing from the end of the file */ public LogFileTailer(File file, long sampleInterval, boolean startAtBeginning) { this.logfile = file; this.sampleInterval = sampleInterval; } public void addLogFileTailerListener(LogFileTailerListener l) { this.listeners.add(l); } public void removeLogFileTailerListener(LogFileTailerListener l) { this.listeners.remove(l); } protected void fireNewLogFileLine(String line) { for (Iterator i = this.listeners.iterator(); i.hasNext();) { LogFileTailerListener l = (LogFileTailerListener) i.next(); l.newLogFileLine(line); } } public void stopTailing() { this.tailing = false; } public void run() { long filePointer = 0; if (this.startAtBeginning) { filePointer = 0; } else { filePointer = this.logfile.length(); } try { this.tailing = true; RandomAccessFile file = new RandomAccessFile(logfile, "r"); while (this.tailing) { long fileLength = this.logfile.length(); if (fileLength < filePointer) { file = new RandomAccessFile(logfile, "r"); filePointer = 0; } if (fileLength > filePointer) { file.seek(filePointer); String line = file.readLine(); while (line != null) { this.fireNewLogFileLine(line); line = file.readLine(); } filePointer = file.getFilePointer(); } sleep(this.sampleInterval); } file.close(); } catch (IOException e) { logger.error("IO ERROR:", e); } catch (InterruptedException e) { logger.error("InterruptedException:", e); } } }

使用起来很简单，只需要实现一个LogFileTailerListener后加入到tailer，这个tailer会去自动去执行。 类似： 

> public class Tail implements LogFileTailerListener { /** * The log file tailer */ private LogFileTailer tailer; /** * Creates a new Tail instance to follow the specified file */ public Tail(String filename) { tailer = new LogFileTailer(new File(filename), 1000, false); tailer.addLogFileTailerListener(this); tailer.start(); } /** * A new line has been added to the tailed log file * * @param line The new line that has been added to the tailed log file */ public void newLogFileLine(String line) { System.out.println(line); } /** * Command-line launcher */ public static void main(String[] args) { if (args.length < 1) { System.out.println("Usage: Tail "); System.exit(0); } Tail tail = new Tail(args[0]); } }

上面的代码出处：http://www.informit.com/guides/content.aspx?g=java&seqNum;=226，我(54chen)对其进行了一些修改，保障出现异常时不会出现死循环。 下载修改后的可用的代码： <http://ishare.iask.sina.com.cn/f/15999043.html>

## Comments

**[woshishei](#15575 "2013-06-08 16:42:42"):** 这也太扯淡了吧。明明是别人的代码，代码注释里写的都是你自己的名字。

**[54chen](#15576 "2013-06-09 10:46:04"):** 文章不知道仔细读就知道喷，希望你别用我修改过的代码。

**[太行](#13805 "2011-09-17 09:54:52"):** 感谢!

**[agentzh](#13586 "2011-06-07 15:01:39"):** 可以考虑使用 libfam 来监视日志文件的变化，包括内容修改和文件轮转时的移动，而不是自己定时去 poll; 在 linux 上直接有 inotify 可用。

**[54chen](#13587 "2011-06-08 14:59:31"):** 微博上有位兄弟提到java7有新特性，在nio包里，应该是直接用了系统的notification http://download.oracle.com/javase/tutorial/essential/io/notification.html

