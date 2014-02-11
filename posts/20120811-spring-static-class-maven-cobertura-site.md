title: Testing and Mocking in spring2.5: Static class、maven site、cobertura
link: http://www.54chen.com/java-ee/spring-static-class-maven-cobertura-site.html
author: 54chen
description: 
post_id: 2137
created: 2012/08/11 12:52:08
created_gmt: 2012/08/11 04:52:08
comment_status: open
post_name: spring-static-class-maven-cobertura-site
status: publish
post_type: post

# Testing and Mocking in spring2.5: Static class、maven site、cobertura

![mock spring static class cobertura](http://img03.taobaocdn.com/imgextra/i3/T13gR3Xa4cXXcFX9c3_050845.jpg) **spring 2.5 与 junit 4.4** Spring 2.5.x不能用JUnit 4.5+.如果把使用了spring-test 2.5 的testcase升级到 junit 4.5会得到NoClassDefFoundError: 

> org.apache.maven.surefire.booter.SurefireExecutionException: org/junit/Assume$AssumptionViolatedException; nested exception is java.lang.NoClassDefFoundError: org/junit/Assume$AssumptionViolatedException java.lang.NoClassDefFoundError: org/junit/Assume$AssumptionViolatedException 

这是spring-test 2.5.x的一个bug. 其与JUnit 4.5不一致. 只能使用4.0-4.4. 或者就是自己打patch. <http://jira.springframework.org/browse/SPR-5145> **EasyMock 与 static Class** EasyMock未提到是否支持static methods.功能类经常是static的，是因为没有上下文环境依赖，但如果一定要mock他们，可以选择powerMock. **PowerMock、static Class、junit、spring** PowerMock 需要你添加 '@RunWith(PowerMockRunner.class)'.但 sping-test 需要你添加 '@RunWith(SpringJUnit4ClassRunner.class)'. <http://www.jayway.com/2010/12/28/using-powermock-with-spring-integration-testing/> 如果junit 4.7,PowerMock将RunWith替换为: @Rule public PowerMockRule rule = new PowerMockRule(); http://www.infoq.com/news/2009/07/junit-4.7-rules **冲突** Spring 2.5.x不能用JUnit 4.5+, 而PowerMockRule 只能用 JUnit 4.7. 基本上在Java + Spring 2.5 & JUnit 4.4环境下没啥简单的办法去mock static class了. **maven site 与 cobertura** 当你运行 'mvn site' or 'mvn sonar:sonar', 在spring项目中你可能会得到: NoSuchBeanDefinitionException 第一个答案: 在test的application文件中添加: 

  1. <aop:config proxy-target-class="true"/>  

第二个答案: 'mvn site' 与 'mvn sonar:sonar' 会生成类在目录 ./generated-classes/cobertura 中.确保你想autowire的类都在正确的目录下扫描. \----------- **spring 2.5 and junit 4.4** Spring 2.5.x is incompatible with JUnit 4.5+.If you upgrade to junit 4.5,all of your test case which uses the SpringJUnit4ClassRunner will get a NoClassDefFoundError: 

> org.apache.maven.surefire.booter.SurefireExecutionException: org/junit/Assume$AssumptionViolatedException; nested exception is java.lang.NoClassDefFoundError: org/junit/Assume$AssumptionViolatedException java.lang.NoClassDefFoundError: org/junit/Assume$AssumptionViolatedException 

It is an known issue with spring-test 2.5.x. It is incompatible with JUnit 4.5. Use 4.0-4.4. Or you can try the patch in the issue tracker. <http://jira.springframework.org/browse/SPR-5145> **EasyMock and static Class** EasyMock doesn't mention static methods.Utility methods should be most of the time static as they have no context and no dependencies.But when you need to mock it,you can you choose PowerMock. **PowerMock、static Class、junit、spring** PowerMock need you add '@RunWith(PowerMockRunner.class)' to your test code.But sping-test need you add '@RunWith(SpringJUnit4ClassRunner.class)'. <http://www.jayway.com/2010/12/28/using-powermock-with-spring-integration-testing/> With junit 4.7,PowerMock can replace RunWith to: @Rule public PowerMockRule rule = new PowerMockRule(); http://www.infoq.com/news/2009/07/junit-4.7-rules **conflict** Spring 2.5.x is incompatible with JUnit 4.5+, and that PowerMockRule is compatible with JUnit 4.7. Basically, There isn't an easy way to do this in Java + Spring 2.5 & JUnit 4.4 at the moment. **maven site and cobertura** When you run 'mvn site' or 'mvn sonar:sonar' with spring project,you may get the error: NoSuchBeanDefinitionException The first answer: In your test application context add the line: 

  1. <aop:config proxy-target-class="true"/>  

The second answer: The 'mvn site' or 'mvn sonar:sonar' will generates class in ./generated-classes/cobertura.Make sure the classes that you want to autowire is all scan in right folder.