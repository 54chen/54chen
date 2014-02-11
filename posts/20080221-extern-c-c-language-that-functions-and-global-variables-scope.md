title: extern--C/C++语言中表明函数和全局变量作用范围
link: http://www.54chen.com/web-ral/extern-c-c-language-that-functions-and-global-variables-scope.html
author: admin
description: 
post_id: 15
created: 2008/02/21 15:40:33
created_gmt: 2008/02/21 07:40:33
comment_status: open
post_name: extern-c-c-language-that-functions-and-global-variables-scope
status: publish
post_type: post

# extern--C/C++语言中表明函数和全局变量作用范围

**extern**是C/C++语言中表明函数和全局变量**作用范围**（可见性）的关键字. 它告诉编译器，其**声明**的函数和变量可以在本模块或其它模块中使用。 1。对于extern变量来说，仅仅是一个变量的声明，其并不是在定义分配内存空间。如果该变量定义多次，会有连接错误 2。通常，**在模块的头文件中对本模块提供给其它模块引用的函数和全局变量以关键字extern声明。****也就是说c文件里面定义，如果该函数或者变量与开放给外面，则在h文件中用extern加以声明。所以外部文件只用include该h文件就可以了。**而且编译阶段，外面是找不到该函数的，但是不报错。link阶段会从定义模块生成的目标代码中找到此函数。 3。与extern对应的关键字是static，被它修饰的全局变量和函数只能在本模块中使用。 后面转载，阅读中。。。。。。。 被extern "C"修饰的变量和函数是按照C语言方式编译和连接的； **未加extern “C”声明时的编译方式 **　　首先看看C++中对类似C的函数是怎样编译的。 作为一种面向对象的语言，C++支持函数重载，而过程式语言C则不支持。函数被C++编译后在符号库中的名字与C语言的不同。例如，假设某个函数的原型为： 

void foo( int x, int y );
该函数被C编译器编译后在符号库中的名字为_foo，而C++编译器则会产生像_foo_int_int之类的名字（不同的编译器可能生成的名字不同，但是都采用了相同的机制，生成的新名字称为“mangled name”）。 _foo_int_int这样的名字包含了函数名、函数参数数量及类型信息，C++就是靠这种机制来实现函数重载的。例如，在C++中，函数void foo( int x, int y )与void foo( int x, float y )编译生成的符号是不相同的，后者为_foo_int_float。 同样地，C++中的变量除支持局部变量外，还支持类成员变量和全局变量。用户所[编写](http://www.yesky.com/key/4285/189285.html)程序的类成员变量可能与全局变量同名，我们以"."来区分。而本质上，编译器在进行编译时，与函数的处理相似，也为类中的变量取了一个独一无二的名字，这个名字与用户程序中同名的全局变量名字不同。 **未加extern "C"声明时的连接方式** 假设在C++中，模块A的头文件如下： 

// 模块A头文件　moduleA.h #ifndef MODULE_A_H #define MODULE_A_H int foo( int x, int y ); #endif
在模块B中引用该函数： 

// 模块B实现文件　moduleB.cpp #include "moduleA.h" foo(2,3);
实际上，在连接阶段，连接器会从模块A生成的目标文件moduleA.obj中寻找_foo_int_int这样的符号！ **加extern "C"声明后的编译和连接方式** 加extern "C"声明后，模块A的头文件变为： 

// 模块A头文件　moduleA.h #ifndef MODULE_A_H #define MODULE_A_H extern "C" int foo( int x, int y ); #endif
在模块B的实现文件中仍然调用foo( 2,3 )，其结果是： （1）模块A编译生成foo的目标代码时，没有对其名字进行特殊处理，采用了C语言的方式； （2）连接器在为模块B的目标代码寻找foo(2,3)调用时，寻找的是未经修改的符号名_foo。 如果在模块A中函数声明了foo为extern "C"类型，而模块B中包含的是extern int foo( int x, int y ) ，则模块B找不到模块A中的函数；反之亦然。 所以，可以用一句话概括extern “C”这个声明的真实目的（任何语言中的任何语法特性的诞生都不是随意而为的，来源于真实世界的需求驱动。我们在思考问题时，不能只停留在这个语言是怎么 做的，还要问一问它为什么要这么做，动机是什么，这样我们可以更深入地理解许多问题）： 实现C++与C及其它语言的混合编程。 明白了C++中extern "C"的设立动机，我们下面来具体分析extern "C"通常的[使用技巧](http://www.yesky.com/key/263/160263.html)。 4.extern "C"的惯用法 （1）在C++中引用C语言中的函数和变量，在包含C语言头文件（假设为cExample.h）时，需进行下列处理： 

extern "C" { #include "cExample.h" }
而在C语言的头文件中，对其外部函数只能指定为extern类型，C语言中不支持extern "C"声明，在.c文件中包含了extern "C"时会出现编译语法错误。 笔者编写的C++引用C函数例子工程中包含的三个文件的源代码如下： 

/* c语言头文件：cExample.h */ #ifndef C_EXAMPLE_H #define C_EXAMPLE_H extern int add(int x,int y); #endif /* c语言实现文件：cExample.c */ #include "cExample.h" int add( int x, int y ) { return x + y; } // c++实现文件，调用add：cppFile.cpp extern "C" { #include "cExample.h" } int main(int argc, char* argv[]) { add(2,3); return 0; }
如果C++调用一个C语言编写的.DLL时，当包括.DLL的头文件或声明接口函数时，应加extern "C" {　}。 （2）在C中引用C++语言中的函数和变量时，C++的头文件需添加extern "C"，但是在C语言中不能直接引用声明了extern "C"的该头文件，应该仅将C文件中将C++中定义的extern "C"函数声明为extern类型。 笔者编写的C引用C++函数例子工程中包含的三个文件的源代码如下： 

//C++头文件 cppExample.h #ifndef CPP_EXAMPLE_H #define CPP_EXAMPLE_H extern "C" int add( int x, int y ); #endif //C++实现文件 cppExample.cpp #include "cppExample.h" int add( int x, int y ) { return x + y; } /* C实现文件 cFile.c /* 这样会编译出错：#include "cExample.h" */ extern int add( int x, int y ); int main( int argc, char* argv[] ) { add( 2, 3 ); return 0; }

## Comments

**[admin](#2 "2008-11-16 18:17:20"):** 顶一下看看

**[灰灰](#167 "2009-02-10 09:05:12"):** 混合的时候用extern吧

**[gaohuan](#240 "2009-03-20 09:25:13"):** 误人子弟，根本上理解错误 extern的用法是当然C文件是其它文件定义的

**[cc0cc](#241 "2009-03-20 14:30:14"):** 且不论是否误人子弟，至少我肯定你不是一个真正的读者

**[free](#10631 "2009-06-30 03:41:47"):** //C++头文件 cppExample.h #ifndef CPP_EXAMPLE_H #define CPP_EXAMPLE_H #ifdef __cplusplus extern "C"{ #endif int add( int x, int y ); #ifdef __cplusplus } #endif #endif 加上你的另外两个文件就正确了

**[cc0cc](#10632 "2009-06-30 08:04:34"):** 多谢更正

