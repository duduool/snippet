
Go语言笔记
=========

 Go语言是由Google主导开发的完全开源的编程语言，主要开发人员由贝尔实验室原UNIX开发人员组成，其中就有B语言和UNIX之父（肯.汤普森，它和丹尼斯一起用C语言重写了UNIX系统，而且原贝尔实验室的C编译器就是由汤普森开发的——曾有个小故事，说是汤普森在C编译器中预留了一个BUG，在编译UNIX内核时，可以自动在UNIX系统中留下一个后门，这造成了汤普森可以入侵贝尔实验室里的任何一台UNIX系统）。**`Go语言被称为二十一世纪的、互联网时代的C语言`**。

Go语言被设计为：

    （1）有C语言的速度，高性能；
    （2）能够做系统编程；
    （3）有动态语言的某些特性，如：垃圾回收机制——动态分配的内存会自动释放而不需要手工释放；
    （4）有面向对象的某些特性，如：支持属性和方法；
    （5）能省则省，不需要输入的字符一律可省略不写；
    （6）Erlang的高并发——通过goroutine来实现，用着更简单：只需要在函数或方法调用前添加一个go关键字即可。
    （7）去掉繁琐的语言概念和实现，如：面向对象中的构造、析构、继承、重载、多态等等，但Go通过struct包含、
         interface包含、interface赋值等分别实现了面向对象中的继承、重载（不包括运算符重载，Go不支持运算符重载）、
         多态等功能，而且Go的实现更加简单；
     ........


## 前言

除非特别指明，下文描述指 `GO1` 的语言规范。

在早期（即第一个正式版本GO1之前），GO 实现了gc、6c等编译工具，但从GO1开始，GO不再显示地提供这些工具，而提供了GO工具链go程序，它可以自动化查找依赖并编译大型软件，还可以修复、格式化程序，等等。另外，GO语言也为gcc编写了前端（即gccgo），因此也可以使用gccgo来编译GO程序。但是由于gccgo的滞后性等等原因，并不建议使用gccgo，而是直接使用go工具链，它专门被设计用来编译大型程序的，不需要像C/C++那样还要写大量的Makefile文件，go工具链自动查找、解决依赖关系。
