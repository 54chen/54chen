title: 用java操作bdb的方法记录二
link: http://www.54chen.com/java-ee/bdb-method-of-operation-with-the-java-record-2.html
author: cc0cc
description: 
post_id: 954
created: 2010/02/20 11:17:49
created_gmt: 2010/02/20 03:17:49
comment_status: open
post_name: bdb-method-of-operation-with-the-java-record-2
status: publish
post_type: post

# 用java操作bdb的方法记录二

在科学院之前的一篇文章中，介绍过普通的操作BDB的方法，见： 

# [java使用bdb手记（Berkeley DB Java api记录）](/java-ee/java-using-the-bdb-notes-from-berkeley-db-java-api-records.html)

下文记录的是利用BDB的事务来完成的一个put和一个get方法的代码。其速度与原始的操作方法相当。 ================================================代码段============================== /** * @author zhen.chen * @createTime 2010-2-20 上午10:43:31 */ package com.renren.nuclear.storage; import java.io.File; import java.util.ArrayList; import java.util.List; import com.sleepycat.je.Cursor; import com.sleepycat.je.Database; import com.sleepycat.je.DatabaseConfig; import com.sleepycat.je.DatabaseEntry; import com.sleepycat.je.DatabaseException; import com.sleepycat.je.Environment; import com.sleepycat.je.EnvironmentConfig; import com.sleepycat.je.LockMode; import com.sleepycat.je.OperationStatus; import com.sleepycat.je.Transaction; public class Test { private static Environment env; private static Database db; private static long cacheSize = 1000 * 1024 * 1024; private static String path = "./bdb/"; public static void main(String[] args) { EnvironmentConfig envConfig = new EnvironmentConfig(); envConfig.setTransactional(true); envConfig.setAllowCreate(true); envConfig.setCacheSize(cacheSize); try { env = new Environment(new File(path), envConfig); } catch (DatabaseException e) { } open("nuclear"); //写入 put("aa","aaa"); //取出 get("aa"); } /** * 写入操作 * @author www.54chen.com * @createTime 2010-2-20 上午11:04:43 */ public static void put(String key, String value){ // open("nuclear"); byte[] theKey = key.getBytes(); byte[] theValue = value.getBytes(); OperationStatus status = null; Transaction transaction = null; Cursor cursor = null; boolean succeeded = false; try { transaction = env.beginTransaction(null, null); cursor = db.openCursor(transaction, null); status = cursor.put(new DatabaseEntry(theKey), new DatabaseEntry(theValue)); if (status != OperationStatus.SUCCESS){ // TODO } succeeded = true; } catch (DatabaseException e) { } finally { attemptClose(cursor); if (succeeded) attemptCommit(transaction); else attemptAbort(transaction); } } /** * 取出操作 * @author www.54chen.com * @createTime 2010-2-20 上午11:04:51 */ public static List<String> get(String key){ ArrayList<String> nuclearStorageValue = new ArrayList<String>(); DatabaseEntry queryKey = new DatabaseEntry(); DatabaseEntry value = new DatabaseEntry(); queryKey.setData(key.getBytes()); Cursor cursor = null; try { cursor = db.openCursor(null, null); for(OperationStatus status = cursor.getSearchKey(queryKey, value, LockMode.READ_UNCOMMITTED); status == OperationStatus.SUCCESS; status = cursor.getNextDup(queryKey,value,LockMode.RMW)) { nuclearStorageValue.add(value.getData().toString()); } } catch (DatabaseException e) { } finally { attemptClose(cursor); } return nuclearStorageValue; } /** * 打开DB * @author www.54chen.com * @createTime 2010-2-20 上午11:01:48 */ private static void open(String dbName) { DatabaseConfig dbConfig = new DatabaseConfig(); dbConfig.setAllowCreate(true); dbConfig.setTransactional(true); try { db = env.openDatabase(null, dbName, dbConfig); } catch (DatabaseException e) { e.printStackTrace(); } } private static void attemptClose(Cursor cursor) { try { if (cursor != null) cursor.close(); } catch (DatabaseException e) { // TODO logger } } private static void attemptCommit(Transaction transaction) { try { transaction.commit(); } catch (DatabaseException e) { attemptAbort(transaction); // TODO logger } } private static void attemptAbort(Transaction transaction) { try { transaction.abort(); } catch (Exception e) { // TODO logger } } } ============================================代码结束================================== 代码是用游标操作bdb的例子，在使用的时候可以在其基础上继续进一步修改。 PS：人人网Nuclear系统的BDB引擎将可能会使用这样的操作方法，目前我还在做速度上的调优，待有新的结果将第一时间公布给大家。

## Comments

**[ajavaa](#12154 "2010-02-20 21:19:20"):** 不知道速度怎么样

**[cc0cc](#12162 "2010-02-23 16:57:38"):** 优化配置能达到 1.5w/s速度

