title: 一张图理解Javascript的继承原理
date: 2015-11-20 15:28:14
categories: 编程
tags: [javascript,prototype]
---

图解Javascript基于原型的继承原理。

<!-- more -->

![Javascript Inherits](/res/img/prototype.png)

```js
function Cat() {
    //
}
var myCat = new Cat();
```

# Javascript中的对象
- 在`javascript`中，一切皆对象
- 对象分为三类(图中用三种颜色分别标记三种对象)：
    - 函数对象
    - 原型对象
    - 实例对象
- 函数也用对象来表示，即函数对象
- 原型对象为函数对象的`prototype`指向的对象
- 实例对象是通过`new`操作和构造函数创建出来对象

# 对象之间的关系
- 任何对象都有一个隐藏属性(在Google Chrome中用`__proto__`表示)指向该对象的父对象
    - `Function.prototype`是所有函数对象的原型(1.1标记的箭头)
    - `Object.prototype`是所有原型对象的原型(1.2标记的箭头)
    - 函数对象对应的原型对象是其实例对象的原型(1.3标记的箭头)
- 任何函数对象都有一个`prototype`属性指向对应的原型对象，表示其实例对象的父对象(2标记的箭头)
- 任何原型对象都有一个`constructor`属性指向对应的函数对象(3标记的箭头)
