title: 在C语言中extern enum类型变量
date: 2016-01-26 19:29:29
categories: c/cpp
tags: [a, extern, enum]
---

在C语言中，extern enum类型的用法。

<!-- more -->

问题： 在一个头文件中定义enum变量，然后其他C文件或者H文件中使用该变量，该如何做？

仔细分析一下，其实道理很简单，直接上实例。

# 实例

`def.h`中声明enum类型，
```c
#ifndef DEF_H
#define DEF_H

enum E {
    A, B, C
};

#endif
```

`a.h`文件中定义enum类型变量`e`，
```c
#ifndef A_H
#define A_H

#include "def.h"

enum E e;

void a();

#endif
```

`b.h`、`c.h`里面使用`extern`声明enum变量`e`，
```c
/* b.h */
#ifndef B_H
#define B_H

#include "def.h"

extern enum E e;

void b();

#endif

/* c.h */
#ifndef C_H
#define C_H

#include "def.h"

extern enum E e;

void c();

#endif
```

`a.c`, `b.c`, `c.c`几乎一样，都是实现各自的函数而已，
```c
/* a.c */
#include <stdio.h>
#include "a.h"

void a() {
    printf("a: %d\n", A);
}

/* b.c */
#include <stdio.h>
#include "b.h"

void b() {
    printf("b: %d\n", B);
}

/* c.c */
#include <stdio.h>
#include "c.h"

void c() {
    printf("c: %d\n", C);
}
```

最后是测试代码`main.c`，
```c
#include "a.h"
#include "b.h"
#include "c.h"

int main()
{
    a();
    b();
    c();
    
    return 0;
}
```

# 说明
`def.h`里面声明新的enum类型，`a.h`里面定义新的enum类型变量`e`，
然后在`b.h`, `c.h`中使用该enum类型变量`e`，它们两个只是在使用`e`，
并不重复定义，所以使用`extern`达到此目的。

(over)