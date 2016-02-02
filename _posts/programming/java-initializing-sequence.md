title: Java类、对象初始化顺序
date: 2016-01-28 16:11:57
categories: 编程
tags: [java,static]
---
Java类、对象的初始化顺序。

<!-- more -->
以下面的代码为例来看下Java类、对象的初始化顺序。

```java
package com.test.com;

public class Base {
	private static String staticVar = "base static";
	
	static {
		System.out.println(staticVar);
	}
	
	private String instanceVar = "base instance var";
	
	{
		System.out.println("instance initializer: " + instanceVar);
	}

	public Base() {
		System.out.println("constructor: " + instanceVar);
	}

	static class Sub extends Base {
		private static String staticVar = "sub static";
		
		static {
			System.out.println(staticVar);
		}
		
		private String instanceVar = "sub instance var";
		
		{
			System.out.println("instance initializer: " + instanceVar);
		}

		public Sub() {
			System.out.println("constructor: " + instanceVar);
		}
	}

	public static void main(String[] args) {
		new Sub();
	}
}
```

# 类的初始化
虚拟机在加载类信息时，会首先加载该类的父类（包括接口）并初始化父类，等父类都处理好了，
才会回来加载该类并初始化，`static`的内容会在此阶段处理，而且父类的`static`内容先于子类处理完成。
`static`的内容包括`static variable`和`static initializer`，这些static的内容会按照其在源码中
的顺序进行初始化，这里需要注意的是，变量的声明和赋值，虽然有时声明、赋值在代码中是写在一起的，
但是编译之后，它们是**分开**的，赋值操作会被编译器安插到**类初始化器**（就是那个特殊`<clinit>`函数），
所以最终的`<clinit>`函数包括下面两部分内容：
- 静态变量的赋值操作
- static initializer
它们出现的顺序就是它们在Java代码中出现的顺序。用`javap`反编译`.class`文件就会看到`static {}`这个函数，
可是，它应该叫做`<clinit>`吗，是的，它就是`<clinit>`，只是`javap`在输出的时候给偷偷的转换了，同理，
下面出现的构造函数，即`<init>`函数也是被`javap`给偷偷替换为类名了。看看`javap`的源码就知道了。
```java
// javap的部分源码
// ... m 表示Method对象，classFile显然是Class对象
if (getName(m).equals("<init>")) {
    print(getJavaName(classFile));
    print(getJavaParameterTypes(d, flags));
} else if (getName(m).equals("<clinit>")) {
    print("{}");
} else {
    print(getJavaReturnType(d));
    print(" ");
    print(getName(m));
    print(getJavaParameterTypes(d, flags));
}
// ...
```

# 对象的初始化
对象初始化仍然遵循先父类后子类的顺序。
类有**类初始化器**，对象当然有**对象初始化器**，即`<init>`函数，
`<init>`函数包括三部分：
1. 父类构造函数的调用
2. field的赋值操作(类似于静态变量，声明、赋值被分离了)
3. instance initializer
4. 构造函数
其中，第1部分永远会排到第一位，2、3两部分会按照它们在Java代码中的顺序出现，而第4部分永远位于最后，
下面这两种写法，其字节码是一模一样的，
```java
// 第一种写法
private String instanceVar = "base instance var";
{
    System.out.println("instance initializer: " + instanceVar);
}
public Base() {
    System.out.println("constructor: " + instanceVar);
}

// 第二种写法
private String instanceVar;
public Base() {
    instanceVar = "base instance var";
    System.out.println("instance initializer: " + instanceVar);
    System.out.println("constructor: " + instanceVar);
}
```

不过，需要注意的是存在多个构造函数的情形，此时就会出现`<init>`函数的overload，
每一个`<init>`函数的前三部分都是一样的，只是第四部分会分别对应到各个构造函数。

多说一句，为什么不能直接调用构造函数呢？因为`.class`文件中只有`<init>`方法，
根本没有一个名字跟类名一样的方法。

所以，上面代码的输出就很显然了：
```
base static
sub static
instance initializer: base instance var
constructor: base instance var
instance initializer: sub instance var
constructor: sub instance var
```

(over)