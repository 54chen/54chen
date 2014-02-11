title: java使用bdb手记（Berkeley DB Java api记录）
link: http://www.54chen.com/java-ee/java-using-the-bdb-notes-from-berkeley-db-java-api-records.html
author: cc0cc
description: 
post_id: 757
created: 2009/08/25 18:37:34
created_gmt: 2009/08/25 10:37:34
comment_status: open
post_name: java-using-the-bdb-notes-from-berkeley-db-java-api-records
status: publish
post_type: post

# java使用bdb手记（Berkeley DB Java api记录）

在线Berkeley DB Java Edition API： <http://www.oracle.com/technology/documentation/berkeley-db/je/java/index.html> Berkeley DB是一个开放源代码的内嵌式数据库管理系统，能够为应用程序提供高性能的数据管理服务。应用它程序员只需要调用一些简单的API就可以完成对数据的访问和管理。与常用的数据库管理系统（如MySQL和Oracle等）有所不同，在Berkeley DB中并没有数据库服务器的概念。应用程序不需要事先同数据库服务建立起网络连接，而是通过内嵌在程序中的Berkeley DB函数库来完成对数据的保存、查询、修改和删除等操作。 用java来操作BDB，需要jar包je-3.3.62.jar 

> import java.io.File; import com.sleepycat.je.Database; import com.sleepycat.je.DatabaseConfig; import com.sleepycat.je.DatabaseEntry; import com.sleepycat.je.DatabaseException; import com.sleepycat.je.Environment; import com.sleepycat.je.EnvironmentConfig; import com.sleepycat.je.LockMode; import com.sleepycat.je.OperationStatus; public class MyBerkeleyDB { private Environment env; private Database db; public MyBerkeleyDB() { } public void setUp(String path, long cacheSize) { EnvironmentConfig envConfig = new EnvironmentConfig(); envConfig.setAllowCreate(true); envConfig.setCacheSize(cacheSize); try { env = new Environment(new File(path),envConfig); } catch (DatabaseException e) { e.printStackTrace(); } } public void open(String dbName) { DatabaseConfig dbConfig = new DatabaseConfig(); dbConfig.setAllowCreate(true); try { db = env.openDatabase(null, dbName, dbConfig); } catch (DatabaseException e) { e.printStackTrace(); } } public void close() { try { if(db != null) { db.close(); } if(env != null) { env.close(); } } catch (DatabaseException e) { e.printStackTrace(); } } public String get(String key) throws Exception { DatabaseEntry queryKey = new DatabaseEntry(); DatabaseEntry value = new DatabaseEntry(); queryKey.setData(key.getBytes("UTF-8")); OperationStatus status = db.get(null, queryKey, value, LockMode.DEFAULT); if (status == OperationStatus.SUCCESS) { return new String(value.getData()); } return null; } public boolean put(String key, String value) throws Exception { byte[] theKey = key.getBytes("UTF-8"); byte[] theValue = value.getBytes("UTF-8"); OperationStatus status = db.put(null, new DatabaseEntry(theKey), new DatabaseEntry(theValue)); if(status == OperationStatus.SUCCESS) { return true; } return false; } public static void main(String[] args) { MyBerkeleyDB mbdb = new MyBerkeleyDB(); mbdb.setUp("C:\\\bdb", 1000000); mbdb.open("myDB"); System.out.println("开始向Berkeley DB中存入数据..."); for(int i=0; i<20; i++) { try { String key = "myKey"+i; String value = "myValue"+i; System.out.println("[" + key + ":" + value + "]"); mbdb.put(key , value); } catch (Exception e) { e.printStackTrace(); } } mbdb.close(); }

取出和删除 不言自明