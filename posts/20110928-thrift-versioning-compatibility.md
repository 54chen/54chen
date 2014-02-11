title: thrift最佳实践:版本与兼容
link: http://www.54chen.com/document/thrift-versioning-compatibility.html
author: 54chen
description: 
post_id: 1843
created: 2011/09/28 15:40:58
created_gmt: 2011/09/28 07:40:58
comment_status: open
post_name: thrift-versioning-compatibility
status: publish
post_type: post

# thrift最佳实践:版本与兼容

![facebook,thrift](http://img04.taobaocdn.com/imgextra/i4/T1gzmqXXdpXXcwYVo0_034121.jpg) 十分苦逼的api提供过程。一次提供，终身负责制。 

> "API design is like sex: Make one mistake and support it for the rest of your life." - @joshbloch 

**Structs：** 在许多系统里面，被称做message. 协议随着岁月而演变，如果一个已经存在的消息类型不再满足需求，但是你仍然想使用原来使用thrift文件生成的代码，比如你想在现在的消息类型上增加一个额外的字段。 不用担心人，不搞坏原来的任何代码而更新消息类型，是非常简单的，只需要时刻记录以下的**规则**： 

> 1.不要修改现存的任何已经存在字段的数字标签

> 2.任何新增的字段应该是optional的，这就意味着，所有新定义产生的代码都能解析新老的格式，因为一定会有任何一个标上required的元素。你应该设置可能的默认值给这个元素，让新代码与老数据也能正常协作。与此类似，新数据也能被老代码解析，老的实现解析时会忽略新的字段。尽管如此，未知的字段不能被丢弃，并且如果消息被延迟序列化，未知的字段也会被一起序列化，因此当消息到达新代码的时候，新字段依旧可用。

> 3.当你更新的消息类型里不再包括相关的标签数字时，非required字段可以删除。（把这个废字段改名，比如说加个前缀OBSOLETE_ ，可以防止未来维护.thrift文件人误用原来的数字，这做法可能更好。)

> 4.修改默认值基本上是OK的，但是你记得这个默认值永远不会“串线”。比如，如果程序收到一个在某个字段没有值的消息，程序会看那个版本里定义的默认值。这可能会导致得不到正确的默认值。 

cassandra的thrift定义：http://svn.apache.org/viewvc/cassandra/trunk/interface/cassandra.thrift?view=co fb303的定义：http://svn.apache.org/repos/asf/thrift/trunk/contrib/fb303/if/fb303.thrift scribe的定义：https://github.com/facebook/scribe/blob/master/if/scribe.thrift _from:ThriftThe Missing Guide_