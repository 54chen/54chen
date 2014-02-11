title: erlang学习手记
link: http://www.54chen.com/document/erlang-notes.html
author: 54chen
description: 
post_id: 1694
created: 2011/04/26 17:19:12
created_gmt: 2011/04/26 09:19:12
comment_status: open
post_name: erlang-notes
status: publish
post_type: post

# erlang学习手记

![erlang install notes](http://img04.taobaocdn.com/imgextra/i4/T1_EN.XadjXXbk3DE3_051340.jpg) **ubuntu10.04下erlide for eclipse安装** 网址：http://erlide.sourceforge.net/ 3.5版本的eclipse，直接用url http://erlide.org/update 安装 安装后，还需要erlang的环境： 

> sudo apt-get install erlang

现在还不能打开文件，会报空指针错误，选择window->preferences->erlang->installed runtimes 把location选择到 /usr/lib/erlang 即可使用。 

> [把build automatically关掉，这插件还是不怎么靠谱的，用来写写代码就完了，要编译还是走命令行]

**erlang学习要点：** **（1）-module(xx).** 注意前面的杠（-）和后面的点（.） 类似java的package，文件名要求为xx.erl，与module里名字相同。 **（2）-export([double/1]).** 同样注意杠和点。表示模块内的函数为double，包含一个参数。 扩展：多个声明这样写 -export([fac/1, mult/2]). **（3）模式匹配** xxxx->aaaa; 箭头的用意：节省代码，不需要if...else...对应前面执行箭头后面。 **（4）变量只能单次赋值** 首字母必须大写。 **（5）元子** 以小写字母开头。只是一个名字。类似java的常量。 **（6）元组** {inch,Y} {moscow,{c,-10}} 元组中有元素。 **（7）列表** [{},{},{}] 列表不一定要写在一行，不可以在元子或者整数中间分段。 一个很有用的遍历列表的方法是“|”** （8）guard满足条件之后才会执行后面的** 大于> 小于< 等于== 大于等于>= 小于等于<= 不等于/= **（9）竖线的作用 |** 得到列表中第一个元素： [M1|T1] =[aaa,bbb,ccc]. 在列表头部添加元素： L1=[ddd|T1]. **（10）if case** if condition 1 -> Action 1; condition 4 -> Action 4 end. 最后一个没有分号。 case XXX of xxx-> xxx; yyy-> yyy end. 最后一个条件没有分号。 **（11）io:format** ~p很长的时候断行 ~w输出 ~n回车 **（12）spawn和receive以及!还有self()** spawn启动一个process receive等待来自其它进程的消息 !用来发消息：Pid!Message self()表示正在运行的进程ID **（13）register** 将一个进程名字注册为一个名字。 **（14）头文件** *.hrl -include("xxx.hrl"). **（15）宏** -define(SERV_NODE,messenger@super). ？SERV_NODE **（16）记录** -record(message_to,{to_name,message}). message_to{to_name=aaaa,message="hello"} 将得到{message_to,aaaa,"hello"}