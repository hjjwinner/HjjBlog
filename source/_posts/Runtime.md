layout: 15-7-31-objective-c
title: Objective-C Runtime
date: 2015-07-31 23:48:00
tags:
- iOS
- 我

categories:
- iOS
---
初学 Objective-C(以下简称ObjC) 的人很容易忽略一个 ObjC 特性 —— ObjC Runtime。这是因为这门语言很容易上手，几个小时就能学会怎么使用，所以程序员们往往会把时间都花在了解 Cocoa 框架以及调整自己的程序的表现上。然而 Runtime 应该是每一个 ObjC 都应该要了解的东西，至少要理解编译器会吧

```{bash}
[target doMethodWith:var1];
```
编译成：

```{bash}
objc_msgSend(target,@selector(doMethodWith:),var1);
```

这样的语句。理解 ObjC Runtime 的工作原理，有助于你更深入地去理解 ObjC 这门语言，理解你的 App 是怎样跑起来的。我想所有的 Mac/iPhone 开发者，无论水平如何，都会从中获益的。

ObjC Runtime 是开源的

ObjC Runtime 的代码是开源的，可以从这个站点下载： opensource.apple.com。

这个是所有开源代码的链接： http://www.opensource.apple.com/source/

这个是ObjC rumtime 的源代码: http://www.opensource.apple.com/source/objc4/
4应该代表的是build版本而不是语言版本，现在是ObjC 2.0

动态 vs 静态语言

ObjC 是一种面向runtime(运行时)的语言，也就是说，它会尽可能地把代码执行的决策从编译和链接的时候，推迟到运行时。这给程序员写代码带来很大的灵活性，比如说你可以把消息转发给你想要的对象，或者随意交换一个方法的实现之类的。这就要求 runtime 能检测一个对象是否能对一个方法进行响应，然后再把这个方法分发到对应的对象去。我们拿 C 来跟 ObjC 对比一下。在 C 语言里面，一切从 main 函数开始，程序员写代码的时候是自上而下地，一个 C 的结构体或者说类吧，是不能把方法调用转发给其他对象的。举个例子：


```{bash}
#include < stdio.h > int 
main(int argc, const char **argv[]) { 
printf("Hello World!"); return 0; 
}
```
这段代码被编译器解析，优化后，会变成一堆汇编代码：

```{bash}

.text
 .align 4,0x90
 .globl _main
_main:
Leh_func_begin1:
 pushq %rbp
Llabel1:
 movq %rsp, %rbp
Llabel2:
 subq $16, %rsp
Llabel3:
 movq %rsi, %rax
 movl %edi, %ecx
 movl %ecx, -8(%rbp)
 movq %rax, -16(%rbp)
 xorb %al, %al
 leaq LC(%rip), %rcx
 movq %rcx, %rdi
 call _printf
 movl $0, -4(%rbp)
 movl -4(%rbp), %eax
 addq $16, %rsp
 popq %rbp
 ret
Leh_func_end1:
 .cstring
LC:
 .asciz "Hello World!"

```
然后，再链接 include 的库，完了生成可执行代码。对比一下 ObjC，当我们初学这门语言的时候教程是这么说滴：用中括号括起来的语句，

```{bash}
[self doSomethingWithVar:var1];
```
被编译器编译之后会变成：

```{bash}
objc_msgSend(self,@selector(doSomethingWithVar:),var1);
```
一个 C 方法，传入了三个变量，self指针，要执行的方法 @selector(doSomethingWithVar:) 还有一个参数 var1。但是在这之后就不晓得发生什么了。








