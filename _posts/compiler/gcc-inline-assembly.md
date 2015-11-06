﻿title: GCC内联汇编
date: 2014-11-21 16:23:17
categories: 编译器
tags: 汇编
---

GCC内联汇编(inline assembly)的基本语法如下：

```c
__asm__ __volatile___ (
    "assembly statements"
    : "output statements"
    : "input statements"
    : "modified descriptions"
);
```

`__asm__`表示内联汇编，`__volatile__`表示不要优化代码。
三个冒号后面每一个都可以有多个部分，每个部分用逗号隔开。

<!--more-->

例如，下面是CAS(compare and swap)的实现，

```c
int cas(int *lock, int old, int new) {
    int result;
    __asm__ __volatile___ (
        "lock;
        cmpxchgl %4, %1;
        sete %0;"
        : "=q" (result), "=m" (lock)
        : "m" (lock), "a" (old), "r" (new)
        : "memory"
    );
    return result;
}
```

以下为说明：

- output和input部分，每个都可以有多个语句，语句的格式为：`"限定符" (C语言变量)`
- output部分每一个语句的限定符前都有一个`=`，表示这是输出内容
- 汇编语句部分使用占位符(`%0`到`%9`，最多10个)引用output和input中的变量
- modified descriptions表示哪些内容被修改了，用字符串表示，如`"%eax"`、`"memory"`等
- 冒号引导的三部分，每一部分如果有多条内容，均用逗号隔开
- 有很多限定符，其定义如下表所示。

```c
/*
分类                限定符      描述

通用寄存器
                    "a"        将输入变量放入eax
                    "b"        将输入变量放入ebx
                    "c"        将输入变量放入ecx
                    "d"        将输入变量放入edx
                    "s"        将输入变量放入esi
                    "d"        将输入变量放入edi
                    "q"        将输入变量放入eax，ebx，ecx，edx中的一个
                    "r"        将输入变量放入通用寄存器,即eax,ebx,ecx,edx,esi,edi之一
                    "A"        把eax和edx合成一个64 位的寄存器(use long longs)

内存
                    "m"        内存变量
                    "o"        操作数为内存变量，但其寻址方式是偏移量类型, 也即基址寻址
                    "V"        操作数为内存变量，但寻址方式不是偏移量类型
                    " "        操作数为内存变量，但寻址方式为自动增量
                    "p"        操作数是一个合法的内存地址（指针）

寄存器或内存
                    "g"        将输入变量放入eax，ebx，ecx，edx之一,或作为内存变量
                    "X"        操作数可以是任何类型

立即数
                    "I"        0-31之间的立即数（用于32位移位指令）
                    "J"        0-63之间的立即数（用于64位移位指令）
                    "N"        0-255之间的立即数（用于out指令）
                    "i"        立即数
                    "n"        立即数,有些系统不支持除字以外的立即数,则应使用"n"而非 "i"

匹配
                    "0"        表示用它限制的操作数与某个指定的操作数匹配
                    "1" ...    也即该操作数就是指定的那个操作数，例如"0"
                    "9"        去描述"％1"操作数，那么"%1"引用的其实就是"%0"操作数，
                               注意作为限定符字母的0－9 与指令中的"％0"－"％9"的区别，
                               前者描述操作数, 后者代表操作数。
                     &         该输出操作数不能使用过和输入操作数相同的寄存器

操作数类型
                    "="        操作数在指令中是只写的（输出操作数）   
                    "+"        操作数在指令中是读写类型的（输入输出操作数）

浮点数
                    "f"        浮点寄存器
                    "t"        第一个浮点寄存器
                    "u"        第二个浮点寄存器
                    "G"        标准的80387浮点常数
                     %         该操作数可以和下一个操作数交换位置,
                               例如addl的两个操作数可以交换顺序（当然两个操作数都不能是立即数）
                     #         部分注释，从该字符到其后的逗号之间所有字母被忽略
                     *         表示如果选用寄存器，则其后的字母被忽略
*/
```

该表来自[网址](http://www.cppblog.com/jb8164/archive/2008/02/26/43260.html)。