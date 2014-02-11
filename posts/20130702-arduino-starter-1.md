title: arduino学习手记一：基础知识
link: http://www.54chen.com/_linux_/arduino-starter-1.html
author: 54chen
description: 
post_id: 2347
created: 2013/07/02 15:24:40
created_gmt: 2013/07/02 07:24:40
comment_status: open
post_name: arduino-starter-1
status: publish
post_type: post

# arduino学习手记一：基础知识

**历史** arduino是一个商标。 2005年诞生于意大利一个设计学校。然后学生和老师们一起把它开源了。 教科书上一般说，arduino是一个开源电子原型制作平台，包括一个简单易用的电路板和一个软件开发环境。 它与android除了长得像之外，没有半毛钱关系。 **开发环境** http://arduino.cc 这个算是官方网站吧，可以下载到开发环境。 http://arduino.cc/en/Main/Software 有各种环境下的，我下了一个mac下的。 版本号有1.0.×与1.5.×的，看上去1.0的就够用了，1.5系列的还是beta，怕遇到麻烦。看release note，1.5的似乎在有意兼容更多的东西。 下载就可以用了，一个小小的窗口。 ![arduino](http://img.taobaocdn.com/bao/uploaded/i1/18490026877451529/T197hwFlFgXXXXXXXX_!!2-item_pic.png) **认电路板** 某宝上有各种品牌的版子，不过大家的基准是一样的： 14个数字引脚，这些数字引脚可以在程序中设置是输入还是输出。 GND 地线。 AREF 输入参考电压。 6个模拟输入引脚，这些引脚可以用来读取各种模拟信号的值，比如说读某感应器的值，在程序中转化为0到1023的值。 6个模拟输出引脚。 电路板可以接usb，也可以接外接的电源。 长得像这样，每个引脚下板上写了字，多余的引脚暂时还不知道做什么用的。 ![arduino](http://ww2.sinaimg.cn/bmiddle/663fba41jw1e68gtth2bcj20p118g7ab.jpg) **arduino IDE** arduino的编程语言是一种叫做processing的语言改编而来的。他的作为是当你上传到arduino的时候，自动变成c语言。 **代码**

> #define LED 13 void setup() { pinMode(LED,OUTPUT); } void loop() { digitalWrite(LED,HIGH); delay(50); digitalWrite(LED,LOW); delay(50); digitalWrite(LED,HIGH); delay(50); digitalWrite(LED,LOW); delay(50); digitalWrite(LED,HIGH); delay(50); digitalWrite(LED,LOW); delay(2000); }

一个setup 和一个 loop，是每个ardunio程序的标准格式。setup会执行一次，loop会一直不断地执行。 pinMode函数，是用来指定引脚的作用，输出还是输入。 digitalWrite函数，用来向指定的引脚输入数据（高还是低电压）。 LED灯接到GND和13引脚（LED短脚接地）。 点击arduino IDE上的upload，会看到led灯一闪一闪的。 **公式** 电阻＝电压/电流