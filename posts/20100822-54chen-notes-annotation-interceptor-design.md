title: [54chen开发日记]注解和拦截器实现权限通用模型的设计实践
link: http://www.54chen.com/java-ee/54chen-notes-annotation-interceptor-design.html
author: 54chen
description: 
post_id: 1287
created: 2010/08/22 16:35:20
created_gmt: 2010/08/22 08:35:20
comment_status: open
post_name: 54chen-notes-annotation-interceptor-design
status: publish
post_type: post

# [54chen开发日记]注解和拦截器实现权限通用模型的设计实践

![54chen](http://img03.taobaocdn.com/imgextra/i3/T1uu8JXcxbXXXYIGZ2_043201.jpg) \------------------英文大爱-----------------------         在近期的开发过程中，有使用到拦截器来实现权限控制，使用这种设计方案，可以很好地分离权限与系统本身的功能，让开发过程更加关注系统的核心功能，同时可以很容易做到开发时的任务划分，同时使项目代码的可读性大大提升。先来普及几个相关的名词： 1.**AOP**：是OOP的延续，是（Aspect Oriented Programming）的缩写，意思是面向切面编程，也有叫面向方面编程，不过切面更加形象。可以通过预编译方式和运行期动态代理实现在不修改源代码的情况下给程序动态统一添加功能的一种技术。AOP实际是GoF设计模式的延续，设计模式孜孜不倦追求的是调用者和被调用者之间的解耦，AOP可以说也是这种目标的一种实现。 2.**GoF**：《Design Patterns: Elements of Reusable Object-Oriented Software》（即《设计模式》一书），由 Erich Gamma、Richard Helm、Ralph Johnson 和 John Vlissides 合著（Addison-Wesley，1995）。这几位作者常被称为"四人组（Gang of Four）"，而这本书也就被称为"四人组（或 GoF）"书。话说为首的Erich Gamma，在QCon的大会上54chen也有半面之缘。 **权限模型的常量定义**         一个系统里最常见的需求莫过于权限、角色，事实上往往这样子的需求和系统本身的功能又是没有太多关联的。按照常规的做法，我们需要两个类，一个表明都有什么权限（例如：删除帖子权限、编辑帖子权限，等等）;另一个类表明，各个角色都有什么权限。这样子相当于定义了一个权限和角色模型。 **拦截器与注解**         拦截器（Invocation）在在流行的开源框架中很常见，依赖的技术就是Java的动态代理。许多流行的框架都提供实现拦截器的接口，可以很简单就实现一个拦截器，此文不表如何实现。注解（Annotations）是JAVA在5.0后引入的特性，它引入的目的是为了替代一些简单的配置到java代码里，而不用原来的xml。 **注解请求示例**         一般的框架，都会有一个controller类，以下用伪代码表示： ` public class ThreadsController{ @PriCheckRequired({MemberPrivilegeIdentity.CREATE_THREAD}) public String createThread(){ return "createThread"; } }`         如代码中所示，一个controller里的一个method对应一个url请求（例中所示为创建帖子）。我们只需要在其方法上标注@PriCheckRequired({MemberPrivilegeIdentity.CREATE_THREAD})，PriCheckRequired就是注解，其传递了一个信息MemberPrivilegeIdentity.CREATE_THREAD，这也就是前文说的权限类中的创建帖子权限。 **拦截器工作**         拦截器一般都是实现一个框架提供的接口来实现，常用框架都支持。 可以想像在拦截器里要做的事情： 

> 1.根据规定好的request请求的参数，取到用户属于哪个角色。 2.根据controller中注解，取到当前要判断的权限。 3.对比用户角色是否有注解中的权限，如果有，放行，反之拦截。 

**代码实现** 拦截器的代码实现与框架有关: rose框架如何实现拦截器请看<http://code.google.com/p/paoding-rose/wiki/Rose_Code_Fragment_Interceptor>;另外spring等都可以简单实现拦截器。 **篇末**         这种方式可以做的事情还有许多，例如cache等公共模块的地方，都可以使用，对开发速度、任务划分、代码重用程度都有非常适用的地方。

## Comments

**[xiaokai](#12930 "2010-08-23 09:44:43"):** 顶了, 在看..

