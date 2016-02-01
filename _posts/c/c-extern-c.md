title: extern "C"的用法
date: 2016-02-01 15:36:17
categories: c/cpp
tags: [c, extern]
---
`extern "C" {}`的用法。

<!-- more -->

# 功能
`extern "C" {}`是C++的规范（C编译器无法识别），它告诉C++编译器，这些代码要按照C的规范(rule)来处理。
主要是function name的mingle机制，C++有mingle而C没有。在C++中，源码中的函数名会被编译器混合，鬼才知道
最终二进制文件中的函数命到底是什么，而且不同的编译器有不同的混合方法，这就更没处猜了。
所以要想连接C和C++，必须要有一个机制来保证两者可以相互识别，这就是`extern "C" {}`要做的事情。

注意`extern "C" {}`只是告诉C++编译器要按照C的规范来处理代码，但是这些代码仍然是C++编译器来处理，所以
这些代码不能更C++规范冲突，如不能使用C++的关键字来命令变量，你不能定义一个名为`class`的变量。

# 用法
用法包括两部分：
- 在C代码中调用C++函数
- 在C++代码中调用C函数

## 在C代码中调用C++函数
C++编译器生成的二进制文件中，函数名已经不是头文件中声明的样子了，所以要想让C代码来调用这些函数，必须
保证二进制文件中的函数名跟头文件中的函数名一致，这就要求C++代码中声明函数时使用`extern "C" {}`。

## 在C++代码中调用C函数
C编译器不会进行函数名混合，最终的二进制文件中的函数名就是源码中声明的函数名，但是C++编译器在进行调用时
也是默认进行混合的，所以C++源码中`print();`这个函数调用被编译之后可能会变成`_VprintV()`这么一个调用，
所以必须当C++代码中调用C函数时，必须让C++编译器知道，**这个函数是按照C规范定义的**，这样它就被会被混合了。

注意C库必须是用C编译器编译的，当然也可以用C++编译器编译，不过这样的话那就不叫C库。

# 如何写头文件
总而言之，问题的关键还是C++编译器在**生成**、**调用**函数时都会进行函数名混合，导致找不到符号。
那么该如何操作呢？关键还是头文件的写法。
```c
// header.h
#ifndef HEADER_H
#define HEADER_H

#ifdef __cplusplus
#define EXTERN_C       extern "C"
#define EXTERN_C_BEGIN extern "C" {
#define EXTERN_C_END   }
#else
#define EXTERN_C       /* Nothing */
#define EXTERN_C_BEGIN /* Nothing */
#define EXTERN_C_END   /* Nothing */
#endif

EXTERN_C void foo(void);

EXTERN_C_BEGIN
int bar(int);
EXTERN_C_END

#endif
```
这段代码的好处是，该头文件既可以被C编译器使用，也可以被C++编译器使用，因为`extern "C"`无法被C编译器处理，
所以用宏来分开处理。

具体实现的话，可以用C来实现（可以用C编译器也可以用C++编译器进行编译），也可以用C++来实现，至于使用，
可以在C代码中使用，也可以在C++代码中使用。

最后说明一下，除了函数名混合，C，C++还有一些其他区别，如`void func()`，在C编译器看来，`func`函数可以没有
参数，也可以接受任意多个参数(非可变参数)，但是C++编译器看来，`func`不能接受任何参数。

# 实例
作为一个实例，头文件里面定义接口，然后分别用C、C++来实现，最后分别做测试，在C代码中调用C++库，在C++代码中调用C库。
```c
// def.h 接口
#ifndef DEF_H
#define DEF_H

#ifdef __cplusplus
#define EXTERN_C       extern "C"
#define EXTERN_C_BEGIN extern "C" {
#define EXTERN_C_END   }
#else
#define EXTERN_C       /* Nothing */
#define EXTERN_C_BEGIN /* Nothing */
#define EXTERN_C_END   /* Nothing */
#endif

EXTERN_C_BEGIN
void print();
EXTERN_C_END

#endif
```

然后是C版本的实现，
```c
// my.c
#include <stdio.h>
#include "def.h"
void print() {
    printf("c print\n");
}
```
以及C++版本的实现，
```cpp
// my.cpp
#include <iostream>
#include "def.h"
void print() {
    std::cout << "cpp print" << endl;
}
```

最后是测试，C代码中调用C++库函数，
```c
// test.c
#include "def.h"
int main()
{
    print();
    return 0;
}
```
以及C++代码中调用C库函数，
```cpp
// test.cpp
#include "def.h"
int main()
{
    print();
    return 0;
}
```

Makefile文件，
```make
all: testc testcpp
testc : test.c libmycpp.so
    cc -o testc test.c -L. -lmycpp
libmycpp.so : my.cpp
    g++ -shared -fpic -o libmycpp.so my.cpp
    
testcpp : test.cpp libmyc.so
    g++ -o testcpp test.cpp -L. -lmyc
libmyc.so : my.c
    cc -shared -fpic -o libmyc.so my.c
    
clean : 
    rm -f *.so testc testcpp
```

(over)