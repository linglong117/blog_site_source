title: Java泛型里的协变和逆变
date: 2016-02-25 10:28:04
Caregories: 编程
tags: [java, generics, covariance, contravariance]
---
实例讲解Java泛型里的协变和逆变。

<!-- more -->

# Java泛型里的协变和逆变

通过实例来看问题，

```java
// 定义三个类: Benz -> Car -> Vehicle，它们之间是顺次继承关系
class Vehicle {}
class Car extends Vehicle {}
class Benz extends Car {}

// 定义一个util类，其中用到泛型里的协变和逆变
class Utils<T> {
    T get(List<? extends T> list, int i) {
        return list.get(i);
    }
    
    void put(List<? super T> list, T item) {
        list.add(item);
    }
    
    void copy(List<? super T> to, List<? extends T> from) {
        for(T item : from) {
            to.add(item);
        }
    }
}

// 测试函数
void test() {
    List<Vehicle> vehicles = new ArrayList<>();
    List<Benz> benzs = new ArrayList<>();
    Utils<Car> carUtils = new Utils<>();

    carUtils.put(vehicles, new Car());
    Car car = carUtils.get(benzs, 0);
    carUtils.copy(vehicles, benzs);
}
```

我们只需关注`Utils<Car>.copy()`函数即可，两个参数`from`, `to`均为list，

- 对`from`的要求：其中的对象必须是`Car`或者`Car`的子类，即可以用`Car`来引用这些对象
- 对`to`的要求：它必须可以保存`Car`类型的对象，即其元素的类型必须是`Car`或者`Car`的父类

接下来看看该函数的使用情况，`carUtils.copy(vehicles, benzs);`，参数的含义是：

- `List<? extents Car>`：这个类型集合(`List<Car>`, `List<Benz>`)里的元素可以使用替换原则
- `List<? super Car>`：这个类型集合(`List<Car>`,`List<Vehicle>`)里的元素也可以使用替换原则

都可以使用替换原则了，但是他们有何区别呢？

- `List<? extents Car>`：`List<? extents Car>`与`? extends Car`的序关系是**一致**的
- `List<? super Car>`：`List<? super Car>`与`? super Car`的序关系是**相反**的

其中，`? extends Car`, `? super Car`, `List<? extents Car>`, `List<? super Car>`
均为**类型集合**，序关系小的可以替换序关系大的。其实在类型系统里面，Liskov替换原则可以
进一步推广为： **任何序关系大的类型可以出现的地方，序关系小的类型一定可以出现。**
而继承关系是一种特殊的**序关系**，当然这需要语言的类型系统支持才可以。

# 协变和逆变
## 定义(wikipedia)
- `covariant` if it preserves **the ordering of types** (≤), which orders types from more specific to more generic;
- `contravariant` if it reverses this ordering;
- `bivariant` if both of these apply (i.e., both `I<A>` ≤ `I<B>` and `I<B>` ≤ `I<A>` at the same time);
- `invariant` or `nonvariant` if neither of these applies.

## 理解
设`T`是一个类型集合(type set)，其中的元素是一个个类型，如`Vehicle`, `Car`, `Benz`，
`S<T>`是一个根据`T`生成的类型集合(如`List<T>`)，其中的元素也是一个个类型，如`S<Vehicle>`,
`S<Car>`, `S<Benz>`，那么我们有如下定义，

- 如果集合`S<T>`里的序关系跟集合`T`里的序关系**一致**，那么就说`S<T>`跟`T`是**协变关系**
- 如果集合`S<T>`里的序关系跟集合`T`里的序关系**相反**，那么就说`S<T>`跟`T`是**逆变关系**

然后，根据序关系的**大小**就可以使用**替换原则**了。那函数`Utils<Car>.copy()`的参数
为啥要用`? extends T`，`? super T`而不直接使用`T`呢，`void copy(List<T> to, List<T> from)`，
把`T`替换成`Car`之后，要使用这个函数就只能使用`List<Car>`了，但是很明显，我们完全可以
将一个`List<Benz>` copy 到一个`List<Car>`或者`List<Vehicle>`里面，要怎么解决呢？
当然是使用协变和逆变：

- 对于`from`参数，`? extends T`表示跟`T`满足协变关系的`List<T>`就可以使用替换原则
- 对于`to`参数，`? super T`表示跟`T`满足逆变关系的`List<T>`就可以使用替换原则

这样就不用仅仅局限到`List<Car>`了。

## 协变、逆变使用的时机
然后问题又来了，什么时候使用协变，什么时候使用逆变呢？
仔细观察（`C#`里面已经观察好久了）就会发现，

- 如果只是**读取**的话，那么满足**协变**关系的类型可以使用替换原则
- 如果只是**写入**的话，那么满足**逆变**关系的类型可以使用替换原则

比如上面的函数，`void copy(List<? super Car> to, List<? extends Car> from);`，
从`from`里面读取数据，则完全可以从`List<Car>`，`List<Benz>`里面读取，
而往`to`里面写入数据，则完全可以往`List<Car>`，`List<Vehicle>`里面写入，
所以`from`使用满足协变关系的类型而`to`使用满足逆变关系的类型。事实上，在`C#`，`Kotlin`里，
直接使用`out`, `in`来表示协变关系和逆变关系，比如Kotlin里面这样定义copy函数，
`fun copy(to: List<in Car>, from: List<out Car>)`，然后就可以这样使用了，
- `copy(cars, benzs)`
- `copy(cars, cars)`
- `copy(vehicles, benzs)`
- `copy(vehicles, cars)`

(over)
