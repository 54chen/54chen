title: twitter api 中文文档 [前言][515更新]
link: http://www.54chen.com/architecture/twitter-api-chinese-documents.html
author: cc0cc
description: 
post_id: 591
created: 2009/05/14 23:46:01
created_gmt: 2009/05/14 15:46:01
comment_status: open
post_name: twitter-api-chinese-documents
status: publish
post_type: post

# twitter api 中文文档 [前言][515更新]

[ 翻译：陈臻 本文版本：v1.1 最后修改：2009.5.15 转载请注明原文链接：<http://www.54chen.com/c/591> 原稿：twitter wiki 完成2%]

## 前言

(每个开发者在开始使用api前都必须知道的概念) 

### 开始使用api前必读

(细读这一节，你将掌握大多数经验丰富的开发者知识) 【不知道为什么，这一节的内容在wiki里被人删除了】 

### 每一个开发者都必须知道的事情

(每个twitter api开发者都必须知道的基础知识) 0）FAQ的内容 当你开始开发的时候，熟悉FAQ的内容并且知道问题所在。 1）twitter其实有两份api 目前twitter api存在两个分立的版本。大部分的开发者都混用这两份api来完成开发。将REST和Search的api分离是不理想的，完全是由于历史原因。如果开发周期允许的话，我们打算合并[REST](/c/79)和[Search](http://www.54chen.com/c/399)的api完善之。api预览里的前言部分说明了这段历史。 2）你不能无限次地调用 api的使用频率是有限制的。你可以阅读《我们有个雷管》（这个名字好雷哦）来学习下。 3）此api是完全基于HTTP的 从twitter api检索数据的方法需要发送GET请求。提交、修改或者删除数据使用POST请求。DELETE请求也是可用来删除数据。如果你没有使用正确的方法请求数据，使用特殊HTTP方法的api就返回一个错误。HTTP的返回（有链接）是丰富多彩的。 4）此api是RESTful的源 [twitter](http://www.54chen.com/c/154) api企图确保按照REST的原则来设计。只需要简单修改你请求的扩展上的格式就可以取到你所指定的格式。本文档指明了对每一种方法都有哪些格式是可用的。目前api支持的数据格式有：[XML](http://www.54chen.com/c/370)、JSON、RSS、Atom联合供稿格式。有个别方法只支持其中的某些格式。 5）参数都有确定的期望值 有些api方法有必选的和可选的参数。在发起请求的时候要注意两件事情： 1.参数值必须是utf8编码，并且加上urlencode。 2.翻页的起始值是1不是0 在twitter api中有两个特殊的参数： 1. callback：仅在返回格式是JSON的时候使用，用这个参数指定一个回调函数来包装你的返回。例如：附加&callback=myFancyFunction在你的请求上，将会返回：myFancyFunction(...)。此参数只能包含字母数字和下划线，其他非法的字符将会丢弃。 2. suppress_response_codes：当出现这个参数的时候，所有的返回都将以200的结果返回-甚至是错误的时候。这个参数的存在是为了解决js和flash会拦截所有非200的返回这个问题。一旦使用这个参数，要判断错误就只能依靠解析返回的信息体。请小心使用，因为错误信息可能会改变。 前面提到过，api有些方法通过发送不同的HTTP头可以得到不同的结果。用参数和HTTP头可以得到同样的效果时，优先使用参数。 6）分页限制 REST API的限制 使用时间线REST API，客户端通过page和count参数最多可请求3200条状态。超过这个值将返回一个200状态值并且为空的所请求格式数据。twitter还维护了一个包含一个用户发出的所有tweets的数据库。尽管如此，为了确保网站的性能，做这样人工的限制是恰当的。 Search API的限制 使用Search API，客户端通过page和rpp参数最多可请求1500条状态。一次请求走出这个数量将会返回200状态和所请求格式的空值。这个人工限制对确保搜索系统的性能是恰当的。我们还依靠在允许搜索的更新上指定一个时间来限制了搜索索引的范围。当前这个限制是大约一个月的样子，但因为每天增加的用户用户不得不动态缩减。 7）编码影响到状态发送的字数 twitter api支持utf-8编码。请注意尖括号已经被实体编码（就是编码了html的关键字），以预防使用JSON API输出的嵌入式用户被跨站式脚本攻击。编码的实体将算入这140个字符的限制中。当请求XML时，返回是utf-8编码的。所有不在标准ASCII范围内的符号和字符都将被转义成HTML实体。 8）使用twitter api你所有需要的就是命令行 如果你的系统里有curl，那你已经拥有了最好的使用twitter api的工具。这有一些例子： 1.使用RSS格式取到公共时间线，不需要认证：curl <http://twitter.com/statuses/public_timeline.rss> 2.使用XML格式取到所有你follow的人的更新，需要认证：curl -u username:_password_ <http://twitter.com/statuses/friends_timeline.xml> 3.仅查看2中的头信息：curl --head _-u username_:_password_ <http://twitter.com/statuses/friends_timeline.xml> 4.发送一个状态并且取得JSON返回：curl -u username:_password_ -d status="_your message here_" <http://twitter.com/statuses/update.json> 学习更多相关的curl和api请等候翻译。 9）这里有几乎每一种编程语言的twitter api库 社区里创建了许多的api库并且乐于分享。 

### API概述

(过去、现在和将来的twitter api) twitter api包含了两部分，这完全是由于历史原因。最初的时候Summize公司是一家独立的公司专门给twitter数据提供搜索功能。Summize后来被收购，更名为Twitter Search。更名网站很容易，但要完全整合Twitter Search和它的api到Twitter的底层代码却很难。统一api已经被我们提上日程，但Search api和REST api将会作为独立的实体一直到资源允许。 twitter REST api允许开发者得到核心的twitter数据。这包括更新时间，状态数据，用户信息。Search api提供给开发者与Twitter Search和趋势数据。开发者对于这两者需要注意的是频率限制和输出格式的影响。 想学习更多的api方法和数据，请阅读 twitter api技术文档（还没译）。 其们公示目前的缺陷和改进情况。当前的缺陷和改进事项可以看issue list。长期目标会记录在路线图文档中。（有两个连接没有译）。 API会定期更新并增加新的功能。偶尔会去掉一些功能。所有这些更新都会在以下的地方公布： 

  * @twitterapi
  * Google group
  * Changelog

### 频率限制

## Comments

**[hongz](#4330 "2009-05-20 10:32:11"):** 要么你走在市场前面去了，要么你落在市场后面

**[新](#4343 "2009-05-22 11:40:05"):** 这个网站很好玩啊，很有意思，发展前景肯定不错

**[lele.shu](#10664 "2009-07-20 08:48:33"):** 您什么时间有空，能不能向您请教些twitter方面的事情？

**[cc0cc](#10665 "2009-07-20 10:42:06"):** 您有问题可以直接在这里提问

**[sm0king](#11908 "2009-10-11 14:09:18"):** 期待下上面的文章啊....

**[cc0cc](#11923 "2009-10-13 23:09:05"):** 欢迎常来玩

**[54chen](#12673 "2010-08-14 13:52:08"):** 应该是放在参数中，就是一次普通的post请求，参数是status，值是内容。

**[Nikon Lenses](#12663 "2010-08-13 18:35:29"):** 问个问题：例如update.json里面需要参数status即微博内容，请问该参数是放在Body还Header中呢？

