title: 普及帖：Linux 下编译C程序
link: http://www.54chen.com/_linux_/%e6%99%ae%e5%8f%8a%e5%b8%96%ef%bc%9alinux-%e4%b8%8b%e7%bc%96%e8%af%91c%e7%a8%8b%e5%ba%8f.html
author: cc0cc
description: 
post_id: 356
created: 2008/12/19 10:28:02
created_gmt: 2008/12/19 02:28:02
comment_status: open
post_name: %e6%99%ae%e5%8f%8a%e5%b8%96%ef%bc%9alinux-%e4%b8%8b%e7%bc%96%e8%af%91c%e7%a8%8b%e5%ba%8f
status: publish
post_type: post

# 普及帖：Linux 下编译C程序

GNU 编译器集（其前身为GNU C 编译器）诞生于1987年。当时Richard Stallman（GNU 项目的创办人）想要创建一个编译器，它可以满足他定义的“自由软件”概念，并可用来编译 GNU 项目发布的其他软件。GNU C 编译器迅速在自由软件社区中流行开来，而且以其健壮性和可移植性而闻名。它已成为许多集成开发工具的基础，被世界各地的发行商应用在 Linux 和其他操作系统之上。   GCC 已不再是主要针对GNU项目自身的软件的小型 C 语言编译器了。如今，它已支持了许多不同的语言，包括 C、C++、Ada、Fortran、Objective C，甚至还有Java。事实上，现代 Linux 系统除了可以自豪地炫耀那些由 GNU 工具直接支持的语言以外，它还支持大量其他语言。日益流行的脚本语言 Perl、Python 和 Ruby，以及正在不断发展的mono 可移植C#实现的确有助于冲淡人们对 Linux 编程的传统看法，但这完全是另外一个问题了。 Linux 内核和许多其他自由软件以及开放源码应用程序都是用 C 语言编写并使用 GCC 编译的。 1\. 编译单个源文件 为了进行测试，你可以创建“Hello World”程序： 

> #include <stdio.h> #include <stdlib.h> int main(int argc, char **argv) { printf(”Hello world!\n”); exit(0); }

使用如下命令编译并测试这个代码： `# gcc -o hello hello.c # ./hello Hello wordl!` 在默认情况下产生的可执行程序名为a.out，但你通常可以通过 gcc 的“-o”选项来指定自己的可执行程序名称。 2\. 编译多个源文件 源文件message.c包含一个简单的消息打印函数： 

> #include <stdio.h> void goodbye_world(void) { printf(”Goodbye, world!\n”); }

使用gcc的“-c”标记来编译支持库代码： `# gcc -c message.c` 这一过程的输出结果是一个名为message.o的文件，它包含适合连接到一个较大程序的已编译目标代码。 创建一个简单的示例程序，它包含一个调用goodbye_world的main函数 

> #include <stdlib.h> void goodbye_world(void): int main(int argc, char **argv) { goodbye_world(); exit(0); }

使用GCC编译这个程序： `# gcc -c main.c` 现在有了两个目标文件： message.o 和 main.o 。它们包含能够被 Linux 执行的目标代码。要从这个目标代码创建Linux可执行程序，需要再一次调用 GCC 来执行连接阶段的工作： # gcc -o goodbye message.o main.o 运行编译结果： `# ./goodbye Goodbye, world!` 前面这些单独的步骤也可以简化为一个命令，这是因为 GCC 对如何将多个源文件编译为一个可执行程序有内置的规则。 `# gcc -o goodbye message.c main.c # ./goodbye Goodbye, world!` 3\. 使用外部函数库 GCC 常常与包含标准例程的外部软件库结合使用，几乎每一个 Linux 应用程序都依赖于由 GNU C 函数库 GLIBC。 应用外部函数库的例子： 

> #include <stdio.h> #include <stdlib.h> #include <math.h> #define MAX_INPUT 25 int main(int agrc, char **argv) { char input[MAX_INPUT]; double angle; printf(”Give me an angle (in radians) ==>”); if(!fgets(input, MAX_INPUT, stdin)){ perror(”an error occurred.\n”); } angle = strtod(input, NULL); printf(”sin(%e) = %e\n”, angle, sin(angle)); return 0; }

编译命令： `# gcc -o trig -lm trig.c` GCC 的”-lm”选项，它告诉 GCC 查看系统提供的数学库（libm）。因为Linux和UNIX的系统函数库通常以”lib”为前缀，所以我们假设它存在。真正的函数库位置随系统的不同而不同，但它一般会位于目录/lib或/usr/lib中，在这些目录中还有数以百计的其他必需的系统函数库。 4\. 共享函数库与静态函数库 Linux系统上的函数库分为两种不同的类型：共享的和静态的 静态函数库：每次当应用程序和静态连接的函数库一起编译时，任何引用的库函数中的代码都会被直接包含进最终的二进制程序。 共享函数库：包含每个库函数的单一全局版本，它在所有应用程序之间共享。这一过程背后所涉及的机制相当复杂，但主要依靠的是现代计算机的虚拟内存能力，它允许包含库函数的物理内存安全地在多个独立用户程序之间共享。 使用共享函数库不仅减少了文件的容量和 Linux 应用程序在内存中覆盖的区域，而且它还提高了系统的安全性。一个被许多不同程序同时调用的共享函数库很可能会驻留在内存中，以在需要使用它时被立即使用，而不是位于磁盘的交换分区中。这有助于进一步减少一些大型 Linux 应用程序的装载时间。 将上面的 message.c 作为共享库函数使用的例子： `# gcc -fPIC -c message.c` “PIC”命令行标记告诉 GCC 产生的代码不要包含对函数和变量具体内存位置的引用，这是因为现在还无法知道使用该消息代码的应用程序会将它连接到哪一段内存地址空间。这样编译输出的文件 message.o 可以被用于建立共享函数库，我们只需使用gcc的“-shared”标记即可： `# gcc -shared -o libmessage.so message.o` 将上面的mian.c使用共享库函数ligmessage.so编译： `# gcc -o goodbye -lmessage -L. message.o` “-lmessage”标记来告诉 GCC 在连接阶段引用共享函数库 libmessage.so 。“-L.”标记告诉 GCC 函数库可能位于当前目录中，否则 GNU 的连接器会查找标准系统函数库目录，在本例的情况下，就找不到可用的函数库了。 此时运行编译好的goodbye会提示找不到共享函数库： `#./goodbye ./goodbye: error while loading shared libraries: libmessage.so: cannot open shared object file: No such file or directory` 可以使用命令 ldd 来发现一个特定应用程序需要使用的函数库。ldd搜索标准系统函数库路径并显示一个特定程序使用的函数库版本。 

> #ldd goodbye linux-gate.so.1 =>  (0×00493000) libmessage.so => not found libc.so.6 => /lib/libc.so.6 (0×0097c000) /lib/ld-linux.so.2 (0×0095a000)

库文件 libmessage.so 不能在任何一个标准搜索路径中找到，而且系统提供的配置文件 /etc/ld.so.conf 也没有包含一个额外的条目来指定包含该库文件的目录。 需要设置一个环境变量LD_LIBRARY_PATH来制定额外的共享函数库搜索路径， `# export LD_LIBRARY_PATH=`pwd` # ldd goodbye linux-gate.so.1 =>  (0x002ce000) libmessage.so => /tmp/cpro/libmessage.so (0x00b0f000) libc.so.6 => /lib/libc.so.6 (0x0097c000) /lib/ld-linux.so.2 (0x0095a000)` 运行程序 `# ./goodbye Goodbye, world!` gcc在命令行上经常使用的几个选项是： -c   只预处理、编译和汇编源程序，不进行连接。编译器对每一个源程序产生一个目标文件。 -o file  确定输出文件为file。如果没有用-o选项，缺省的可执行文件的输出是 a.out，目标文件和汇编文件的输出对source.suffix分别是source.o和source.s，预处理的C源程序的输出是标准输出stdout。 -Dmacro或-Dmacro=defn   其作用类似于源程序里的#define。例如：% gcc -c -DHAVE_GDBM -DHELP_FILE=\”help\” cdict.c其中第一个- D选项定义宏HAVE_GDBM，在程序里可以用#ifdef去检查它是否被设置。第二个-D选项将宏HELP_FILE定义为字符串“help”（由于反斜线的作用，引号实际上已成为该宏定义的一部分），这对于控制程序打开哪个文件是很有用的。

## Comments

**[玉米疯收](#11855 "2009-08-27 10:06:10"):** 学习啦，呵呵。

