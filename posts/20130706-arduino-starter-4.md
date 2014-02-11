title: arduino学习手记四：串行编程
link: http://www.54chen.com/_linux_/arduino-starter-4.html
author: 54chen
description: 
post_id: 2361
created: 2013/07/06 15:19:43
created_gmt: 2013/07/06 07:19:43
comment_status: open
post_name: arduino-starter-4
status: publish
post_type: post

# arduino学习手记四：串行编程

前面几节介绍了一些常见的传感器使用办法，许多人一定会有一种困惑，如果要做一个arduino发微博的例子得怎么做？ 本节不是教发微博的。 通过在pc上运行的程序去访问互联网，然后把数据通过usb返给arduino的做法，就是利用了串口的编程办法。 在第一节里（<http://www.54chen.com/_linux_/arduino-starter-1.html>），我们说到了arduino使用的主要语言是processing。 以下例子均在mac下试验通过，ubuntu应该差不多，windows不知道应该怎么搞。 **Processing** 这是一门目标群体类似设计师的编程语言，非常的极简，语法类似java，java开发者基本不用学习。其IDE：PDE也是极简到了极致。 PDE(processing IDE)的极简程度令人发指，用习惯了eclipse的同学上来就找的第一个功能就是自动完成，遗憾的是，人家的定义是给设计师们用的，看来工程师们还是回到eclipse的世界去吧。PDE is admirably simple。 十分想用代码自动完成功能的同学，可以试试在eclipse里完成代码： <http://t.cn/zH3b2KP> 其下载和安装都非常简单：http://processing.org/download/下载之后直接使用即可。以mac为例，长得和arduino的IDE基本上一样（应该是说arduino的和它长得基本上一样）。 **开始互动之旅** 打开PDE，代码如下： 

> import processing.serial.*; String cs; String buffer = ""; Serial port; void setup() { println(Serial.list()); String arduinoPort = Serial.list()[5];//如果你运行代码后arduino里没反应，需要来调整这个端口号，据机器不同而不同，看上一行代码打印出来的序号哪一个是arduino就写几。 port = new Serial(this, arduinoPort, 9600); //link to arduino cs = "#hello"; } void draw() { port.write(cs); //send to arduino } 

运行代码，会有提示： 

> To use the serial library, first open Applications -> Utilities -> Terminal.app and enter the following: sudo mkdir -p /var/lock sudo chmod 777 /var/lock

打开终端，对var/lock权限修改。 再运行，就可以打开一个框框，啥也做不了。 **arduino上的代码**

> #define LED 11 byte inByte = 0; int pointer = 0; char buffer[6]; void setup(){ pinMode(LED,OUTPUT); Serial.begin(9600); // open port to 9600 } void loop(){ if(Serial.available() > 0){ inByte = Serial.read(); if(inByte == '#'){ while(pointer < 5){ buffer[pointer] = Serial.read(); } } digitalWrite(LED,HIGH); } }

连线： 在数字11连一220欧电阻加一LED灯。 效果： 启动arduino时，灯不亮，再运行电脑上的代码之后，灯亮了。 **通过arduino通知电脑** 在arduino中使用Serial.write，在电脑上用port.read()。 ![arduino processing](http://ww4.sinaimg.cn/bmiddle/663fba41jw1e6d4imgv9xj20rs0xc42o.jpg)