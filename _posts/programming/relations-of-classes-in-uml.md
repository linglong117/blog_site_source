﻿title: UML中类之间的关系：依赖, 关联, 聚合, 组合, 继承, 实现
date: 2014-12-04 13:47:30
categories: 编程
tags: [UML, PlantUML]
---

UML中类之间的关系总结。

<!--more-->

在UML类图中，类之间大致有如下几种关系，

- 横向比较
    - 依赖关系(dependency)
    - 关联关系(association)
    - 聚合关系(aggregation)
    - 组合关系(composition)
- 纵向比较
    - 继承关系(extention)
    - 实现关系(implementation)

举例如下图，

![Relations of Classes in UML](/res/img/relations_of_classes_in_uml.png)

从依赖关系到组合关系，耦合度逐渐加强，继承关系和实现关系跟组合关系类似，
都是耦合性最强的。所谓类之间的关系，模拟的就是现实世界中各个对象之间的关系，
类在现实中是不存在的，存在的只有对象，这些关系准确来说应该是对象之间的关系，
但是类是对象的模板，所以对象之间的关系抽象出来就成了类之间的关系了。
下面逐个介绍各个关系。

需要注意的是，关系是相对的，看待这些关系需要有个角度，比如从人的生物学角度
来看，Head对于人的关系显然要比CreditCard对于人的关系更加紧密，你可以不知道
信用卡为何物，但是你不能没有脑袋。不过要是换个角度，比如我们正准备创建一套
银行结算系统，人变成了银行的客户，此时CreditCard便成了客户的不可分割的一部
分，相比起客户的脑袋，它的信用卡更值得我们关注。所以讨论类之间的关系，我们
首先要选择一个角度，下面的讲解我们就从_**人的生物学特征**_这个角度去看，所以脑袋
是Human不可分割的一部分，而信用卡则可有可无。

另外，注意UML中各种关系使用的_**连接方式(箭头样式以及箭头方向)**_。

#### 依赖关系(dependency)
依赖关系是耦合性最弱的一种关系，如上图所展示的那样，Human想去旅行(travel)
就需要借助交通工具(Vehicle)，此时人和交通工具之间便产生了一种关系，但是这种
关系是短暂的而且非常之薄弱，甚至可能是一次性的，你打车一个大巴车，可能一辈子
也就跟这个车产生过这一次联系，此后再无瓜葛。

依赖关系一般以局部变量，函数参数，静态函数调用等形式出现，如

```java 在Java中展示依赖关系
// 对于Human类来说，旅行结束之后就再也用不到Vehicle了
class Human {
    void travel(Vehicle vehicle) { /* ... */ }
}
```

#### 关联关系(association)
关联关系比依赖关系增强了一步，联系现实，比如雇员和公司之间的关系，两者之间
产生了一种长久的关系，它们之间更像是一种平等的、合作的关系。关联关系可以是
单向的也可以是双向的。关联关系以及后面的聚合关系、组合关系一般都是通过属性
来产生关联。

#### 聚合关系(aggregation)
聚合关系是关联关系的一种，但是它比关联关系更进一步。想想你和你的信用卡之间
的关系，你**拥有**你的信用卡，这是一种单向关系、一种拥有关系。聚合关系一般
通过属性来产生关联，并进如此，拥有者一方(Human)还要负责管理被拥有者一方(CreditCard)
的生命周期。

#### 组合关系(composition)
组合关系也是关联关系的一种，但是它比关联关系更进一步，不仅如此，它比聚合关系
都更加紧密。Human和公司之间是一种平等的关系，两者谁都可以离开谁。Human和信用卡
之间是一种拥有关系，Human可以离开信用卡而存在，但是信用卡一旦离开Human就废物一
块了，你总不能让狗去用信用卡吧。但是，比起组合关系，它们还都不够紧密，
Human和Head之间是一种组合关系，即Head是Human的组成部分，两者谁也离不开谁，
一旦分离，Human便不再是Human(死Human？！)，Head也就没有什么用处了。**两者的生命周期
是等同的。**

注意，现实世界中组合关系的双方是不可以分离的，一旦分离两者便都成了废物，但是
UML中却允许一方消失之后另一方还可以转借他人，就像心脏从一个人身上移植到另一个
人身上一样。但是同一个心脏不能同时存在于两个人身上。相比之下，一个人却可以随意
更换信用卡，一个公司也可以解雇一个雇员之后再雇佣其他雇员。

#### 继承关系、实现关系
继承关系和实现关系就容易理解的多了，继承就是子类、父类之间的关系，而实现则是
类与接口之间的关系，这些非常明确。

(over)