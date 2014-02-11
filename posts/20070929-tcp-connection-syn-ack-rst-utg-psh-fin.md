title: TCP连接:SYN ACK RST UTG PSH FIN
link: http://www.54chen.com/_linux_/tcp-connection-syn-ack-rst-utg-psh-fin.html
author: admin
description: 
post_id: 34
created: 2007/09/29 18:14:32
created_gmt: 2007/09/29 10:14:32
comment_status: open
post_name: tcp-connection-syn-ack-rst-utg-psh-fin
status: publish
post_type: post

# TCP连接:SYN ACK RST UTG PSH FIN

**TCP的三次握手**是怎么进行的：发送端发送一个SYN=1，ACK=0标志的数据包给接收端，请求进行连接，这是第一次握手；接收端收到请求并且允许连接的话，就会发送一个SYN=1，ACK=1标志的数据包给发送端，告诉它，可以通讯了，并且让发送端发送一个确认数据包，这是第二次握手；最后，发送端发送一个SYN=0，ACK=1的数据包给接收端，告诉它连接已被确认，这就是第三次握手。之后，一个TCP连接建立，开始通讯。 *SYN：同步标志 同步序列编号(Synchronize Sequence Numbers)栏有效。该标志仅在三次握手建立TCP连接时有效。它提示TCP连接的服务端检查序列编号，该序列编号为TCP连接初始端(一般是客户端)的初始序列编号。在这里，可以把 TCP序列编号看作是一个范围从0到4，294，967，295的32位计数器。通过TCP连接交换的数据中每一个字节都经过序列编号。在TCP报头中的序列编号栏包括了TCP分段中第一个字节的序列编号。   *ACK：确认标志 确认编号(Acknowledgement Number)栏有效。大多数情况下该标志位是置位的。TCP报头内的确认编号栏内包含的确认编号(w+1，Figure-1)为下一个预期的序列编号，同时提示远端系统已经成功接收所有数据。 *RST：复位标志 复位标志有效。用于复位相应的TCP连接。 *URG：紧急标志 紧急(The urgent pointer) 标志有效。紧急标志置位， *PSH：推标志 该标志置位时，接收端不将该数据进行队列处理，而是尽可能快将数据转由应用处理。在处理 telnet 或 rlogin 等交互模式的连接时，该标志总是置位的。 *FIN：结束标志 带有该标志置位的数据包用来结束一个TCP回话，但对应端口仍处于开放状态，准备接收后续数据。 .TCP的几个状态对于我们分析所起的作用。在TCP层，有个FLAGS字段，这个字段有以下几个标识：SYN, FIN, ACK, PSH, RST, URG.其中，对于我们日常的分析有用的就是前面的五个字段。它们的含义是：SYN表示建立连接，FIN表示关闭连接，ACK表示响应，PSH表示有 DATA数据传输，RST表示连接重置。其中，ACK是可能与SYN，FIN等同时使用的，比如SYN和ACK可能同时为1，它表示的就是建立连接之后的响应，如果只是单个的一个SYN，它表示的只是建立连接。TCP的几次握手就是通过这样的ACK表现出来的。但SYN与FIN是不会同时为1的，因为前者表示的是建立连接，而后者表示的是断开连接。RST一般是在FIN之后才会出现为1的情况，表示的是连接重置。一般地，当出现FIN包或RST包时，我们便认为客户端与服务器端断开了连接；而当出现SYN和SYN＋ACK包时，我们认为客户端与服务器建立了一个连接。PSH为1的情况，一般只出现在 DATA内容不为0的包中，也就是说PSH为1表示的是有真正的TCP数据包内容被传递。TCP的连接建立和连接关闭，都是通过请求－响应的模式完成的。