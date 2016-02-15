title: Java nested classes
date: 2016-02-14 16:49:14
categories: 编程
tags: [java, inner, nested, class]
---
Java内部类总结。

<!-- more -->
Java规范中定义了好几种内部类，但是根据 **static**, **local**, **anonymous**
三个区分方式就可以将所有内部类划分的明明白白。

![Java nested classes](/res/img/java_nested_class.png)

Java总共定义了4种内部类：
- nested classes(static or not?)
    - **static nested class**
    - non-static nested class(local or not?)
        - **member class**
        - local class(anonymous or not?)
            - **local class**
            - **anonymous class**

等等，下面算是哪一种呢？

```java
class Outer {
    Object mObject = new Object() {
        @Override
        String toString() {
            return "WTF";
        }
    };
}
```

看起来像是`anonymous member class`啊，但是它其实是`anonymous class`，
看官方的定义(oracle docs)：

> There are two additional types of inner classes. 
> You can declare an inner class within the body of a method.
> These classes are known as local classes.
> You can also declare an inner class within the body of a method without naming the class.
> These classes are known as anonymous classes.

另外，不要忘记，field的初始化其实是在构造函数里面进行的，所以上面的`mObject`的初始化(new操作)
其实是在构造函数里面进行的，它是符合匿名内部类的定义的。

(over)