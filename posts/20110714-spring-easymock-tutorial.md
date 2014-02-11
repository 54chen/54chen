title: spring配合easymock搞定不依赖环境的测试用例
link: http://www.54chen.com/java-ee/spring-easymock-tutorial.html
author: 54chen
description: 
post_id: 1768
created: 2011/07/14 08:51:55
created_gmt: 2011/07/14 00:51:55
comment_status: open
post_name: spring-easymock-tutorial
status: publish
post_type: post

# spring配合easymock搞定不依赖环境的测试用例

![spring easymock tutorial](http://img04.taobaocdn.com/imgextra/i4/T1LbWjXndsXXccfjjX_085624.jpg) 在写java的test case的时候，往往有太多依赖环境的情况，WEB项目更甚，如果每次测试都要准备数据库或者其他环境，相当麻烦，换一个人就入手困难，在这种情况下，mock就是最好的选择。 Mock 对象能够模拟领域对象的部分行为，并且能够检验运行结果是否和预期的一致。领域类将通过与 Mock 对象的交互，来获得一个独立的测试环境。 ** 入门**

> //准备一个mock IMocksControl mocksControl = EasyMock.createStrictControl(); //创建录像 aDAO = mocksControl.createMock(AnalyzeResultDAO.class); //录制 输入 期望输出 EasyMock.expect(aDAO.getARByLimit(type)).andReturn(ar); //重放 表示录制结束 EasyMock.replay(aDAO); //调用相关的类 验证结果是否正常 Assert.assertNotNull(analyzeResultBiz.getLastAR(type)); //验证是否使用了上面录制的DAO EasyMock.verify(aDAO);

**提高**

> * 配合spring如何做？ int type = 1; AnalyzeResult ar = new AnalyzeResult(); ar.setName("123"); IMocksControl mocksControl = EasyMock.createStrictControl(); analyzeResultBiz = new AnalyzeResultBiz(); aDAO = mocksControl.createMock(AnalyzeResultDAO.class); EasyMock.expect(aDAO.getARByLimit(type)).andReturn(ar); EasyMock.replay(aDAO); // 用spring提供的方法注入aurowired的字段 ReflectionTestUtils.setField(analyzeResultBiz, "analyzeResultDAO", aDAO, AnalyzeResultDAO.class); Assert.assertNotNull(analyzeResultBiz.getLastAR(type)); EasyMock.verify(aDAO);

这样一录一用，看起来简单方便，实用美观大方，54chen强力推荐。 Ps: 所使用的包： <dependency> <groupId>org.easymock</groupId> <artifactId>easymock</artifactId> <version>2.4</version> </dependency>

## Comments

**[54chen](#13700 "2011-07-27 15:39:28"):** 现在流行不写setter了 有setter可以set

**[bera](#13694 "2011-07-26 10:20:42"):** 这句 ReflectionTestUtils.setField(analyzeResultBiz, “analyzeResultDAO”, aDAO, AnalyzeResultDAO.class); //直接analyzeResultBiz.setAnalyzeResultDAO(aDAO) 就可以了吧

