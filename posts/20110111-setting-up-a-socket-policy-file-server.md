title: [flash]建立socket安全策略文件服务器
link: http://www.54chen.com/web-ral/setting-up-a-socket-policy-file-server.html
author: 54chen
description: 
post_id: 1551
created: 2011/01/11 11:22:09
created_gmt: 2011/01/11 03:22:09
comment_status: open
post_name: setting-up-a-socket-policy-file-server
status: publish
post_type: post

# [flash]建立socket安全策略文件服务器

![flash 安全](http://www.adobe.com/content/dam/Adobe/en/devnet/authors/large/l/logo_flashplayer_lg.jpg.adimg.mw.138.png) **前因** Flash被广泛应用于互联网各个方面，是因为它提供了各种各样的特性，其中很重要的一点就是，可以用flash建立TCP连接到服务器然后交换数据。从网络管理员的观点看，一个互联网的服务器能够连接进到内网里来，是一个很恐怖的事情，所以flash搞出来一个安全策略文件。 **改变** Flash Player 9,0,124,0 版本对这个策略文件进行了两个重大改变：一是所有的端口都需要在策略文件里声明了，以前的版本只需要1024以下的端口需要进行声明；二是可以集中到843端口进行集中式的托管了。Flash Player 9,0,124,0建立的socket连接，会先去请求843端口，如果这个端口没有策略文件，则会去你要连接的端口本身请求策略文件，如果二者都没有，则会被拒绝连接。 **控制过程解读** 1.Flash Player先到请求的843端口请求策略文件，如果没有，进行第2步，如果site-control为none则拒绝掉，如果site-control为all则进行第2步。 2.如果在AS中写了Security.loadPolicyFile() ，（它并不是一定生效的，必须在843端口的声明中允许引用其他的策略文件后才能生效），开始读取策略。 3.最后一步是检测要连接的端口是否有权限，这步检测要在843端口中声明需要检测端口才会去做，如果843声明了而策略文件里没有，则会被拒绝。 `庆亮哥小更正：请求843和你的指定端口后，会访问当前访问域名下是否有crossdomain.xml文件，如果你的端口不是80，那么请求最多可能三次。` **万能脚本** 这个兄弟是adobe的开发人员吧，搞了两个脚本，一个perl的一个是python的，两个都好用，可以很简单地在服务器上搞起来843的守候进程。 像下面这样执行就OK了： 

> `./flashpolicyd.pl --file=../policyfile.xml --port=843 ./flashpolicyd.py --file=../policyfile.xml --port=843`

**猛击哪里下载** [这里](http://www.adobe.com/content/dotcom/en/devnet/flashplayer/articles/socket_policy_files/_jcr_content/articlePrerequistes/multiplefiles/node_1277808777771/file.res/flashpolicyd_v0.6%5B1%5D.zip) **参考文档：** <http://www.adobe.com/devnet/flashplayer/articles/socket_policy_files.html>

## Comments

**[QingliangCn](#13313 "2011-01-11 12:04:52"):** 小更正：请求843和你的指定端口后，会访问当前访问域名下是否有crossdomain.xml文件，如果你的端口不是80，那么请求最多可能三次。

**[54chen](#13315 "2011-01-11 13:23:44"):** 多谢哥更正

