title: arduino学习手记二：写程序控制灯控制蜂鸣器红外
link: http://www.54chen.com/_linux_/arduino-starter-2-led.html
author: 54chen
description: 
post_id: 2352
created: 2013/07/03 11:54:32
created_gmt: 2013/07/03 03:54:32
comment_status: open
post_name: arduino-starter-2-led
status: publish
post_type: post

# arduino学习手记二：写程序控制灯控制蜂鸣器红外

[在上一节中](/_linux_/arduino-starter-1.html)，初步领会了基础精神，对电路板和ide有了一点感觉。 这里的目标是完成一个电路，通过红外传感头，用摇控器控制LED灯和蜂鸣器。 **Arduino Libraries** arduino库函数，是一堆c/c++写的代码库，一般用来做一些固定的事情，对arduino的能力进行扩展，有点像php里的各种扩展，当然你也可以实现自己的库。 在arduino代码中要使用库的时候，依次选sketch->import Library->xxx，它会自动在代码头上加上#include的代码。 同时，也可以下载一个library.zip包，依次选择sketch->import Library->add Library，然后就加入了新的库。 要在arduino IDE环境中删除一个已经引入的库，1.0.5及以上版本的IDE需要去～/documents/arduino/libraries下删除对应的目录(相传windows在C:/.../我的文档/arduino/libraries)。 **IRremote库简介** 官方网站：<http://www.pjrc.com/teensy/td_libs_IRremote.html> 作者：Ken Shirriff 作用：接收和发送红外控制的代码库 **接线** GND接线略去不提，220欧电阻＋LED灯之后，将LED接数字11。 蜂鸣器接数字10。 红外OUT接数字8。 这里要提一下，红外接收头的圆鼓包的一面面向自己，最左一脚为OUT，中间为GND，最右为5V。 如图所示： ![arduino](http://ww3.sinaimg.cn/bmiddle/663fba41jw1e68t7z3gr6j20f10qodgt.jpg) **程序**

> #include  #define LED 11 #define BIZZER 10 #define IR_IN 8 //红外接收 IRrecv irrecv(IR_IN); // 定义IRrecv 对象来接收红外线信号 decode_results results; //解码结果放在decode_results构造的对象results里 void setup() { pinMode(BIZZER,OUTPUT); pinMode(LED,OUTPUT); Serial.begin(9600); irrecv.enableIRIn(); // Start the receiver } void loop() { if (irrecv.decode(&results;)) { Serial.println(results.value, HEX); irrecv.resume(); // Receive the next value digitalWrite(BIZZER,HIGH); digitalWrite(LED,HIGH); delay(50); digitalWrite(BIZZER,LOW); digitalWrite(LED,LOW); delay(1050); } }

**效果** 按摇控器，蜂鸣器响一下同时灯闪一下。IRemote简化了大量的代码。