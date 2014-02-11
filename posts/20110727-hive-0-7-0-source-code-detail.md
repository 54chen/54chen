title: hive 0.7.0代码分析解读
link: http://www.54chen.com/architecture/hive-0-7-0-source-code-detail.html
author: 54chen
description: 
post_id: 1776
created: 2011/07/27 16:05:02
created_gmt: 2011/07/27 08:05:02
comment_status: open
post_name: hive-0-7-0-source-code-detail
status: publish
post_type: post

# hive 0.7.0代码分析解读

![hadoop hive](http://img02.taobaocdn.com/imgextra/i2/T1sUV8Xk4sXXablPM9_104155.jpg) **入口：** bin/hive脚本中，环境检查后执行ext中的cli.sh，进入主类：CliDriver.main。 **CliDriver.main:** 把分号隔开命令进cli.processLine，再进processCmd。 **processCmd:** 处理quit/exit，再处理source，处理！，处理list；else建立CommandProcessor(实现有Driver和各种Processor)，set/dfs/add/delete命令有单独的Processor，剩下的走Driver。 **如果是Driver类型的Processor：** 把cmd发到这个driver的run，再进到compile，在compile中，用一个parseDriver去生成ASTNode(生成使用了antlr，主要过程：经过文法分析器切割，进解析器，出来一个TREE)，这里有细节的compile的过程说明 http://fromheartgo.wordpress.com/2010/04/02/hive%E7%9A%84compile%E8%BF%87%E7%A8%8B%EF%BC%881%EF%BC%89/ ； 根据得到的ASTNode，开始语义分析，把结果设置到一个QueryPlan对象中，初始化一些task放在QueryPlan中； run里的test only代码读了test.serialize.qplan的设置，test状态会把这些查询记录写到文件里；权限检查。 退出complie，在Driver的run中分解执行MR后，退出来到了processCmd: 如果装填一切正常，通过getResults取到MR运行结果。 插一句，54chen最近在各处只谈政治，不谈技术，罪过罪过，只因看不下去了。 **全过程如下：** CliDriver.main > processLine > processCmd >> Driver.run(cmd) > compile >> BaseSemanticAnalyzer >> xxxSemanticAnalyzer（常规select走SemanticAnalyzer） > analyze(sem.analyze) >> SemanticAnalyzer的analyzeInternal方法 >> new Optimizer.optimize（进行列剪裁等优化后生成Task） > genMapRedTasks >> 返回到Driver.run(cmd) >>ret = execute() >> launchTask >> TaskRunner.run > Task.executeTask > ExecDriver.execute > 执行MR（submitJob） >> getResults.