title: intel汇编与AT＆T的区别
link: http://www.54chen.com/web-ral/intel-compile-the-difference-between-at-u0026amp-t.html
author: admin
description: 
post_id: 30
created: 2006/10/26 18:11:57
created_gmt: 2006/10/26 10:11:57
comment_status: open
post_name: intel-compile-the-difference-between-at-u0026amp-t
status: publish
post_type: post

# intel汇编与AT＆T的区别

[文章作者：陈臻 本文版本：v1.0 最后修改：2006.10.26 转载请注明原文链接：<http://www.54chen.com/c/30>] 在设计mynet的时候用到这个，其实二者区别不是太多，总结一下有下面几点： 1.intel的mov 变成了movb movl mov (后面对应8 16 32位的操作位) ) 2.寄存器都加百分号 3.操作都是反过来的：mov al,cl（inter）＝＝movb %cl,%al 而在gcc里的内联汇编也有不同，用起来也是挺好用的，看例～ main(){ char tmp; __asm__( "movb %1, %%cl;" "movb %%cl, %%dl;" "shl $7,%%edx;" "movb %%cl,%%al;" "and $2,%%eax;" "shr $1,%%al;" "or %%eax,%%edx;" "movb %%cl,%%al;" "and $4,%%eax;" "shl $2,%%eax;" "or %%eax,%%edx;" "movb %%cl,%%al;" "and $8,%%eax;" "shl $2,%%eax;" "or %%eax,%%edx;" "movb %%cl,%%al;" "and $0x10,%%eax;" "shl $2,%%eax;" "or %%eax, %%edx;" "movb %%cl,%%al;" "and $0x20,%%eax;" "shr $2,%%al;" "or %%eax,%%edx;" "movb %%cl,%%al;" "and $0x40,%%eax;" "shr $4,%%al;" "or %%eax,%%edx;" "and $0x80,%%ecx;" "shr $6,%%cl;" "or %%ecx,%%edx;" "movb %%dl, %0;" :"=r"(tmp) //tmp变量写出，就是上文中的%0，第0个参数 :"r"(tmp) //tmp写入，就是上文中的%1，第1个参数 :"%ecx","%edx"); //tmp变了; } 这来自一段用过的加密过程中，8位的tmp变量传入了内嵌的汇编中，一通翻江倒海后变了。。。