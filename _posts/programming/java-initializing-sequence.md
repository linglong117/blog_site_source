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
才会回来加载该类并初始化，`static`的内容会在阶段处理，而且父类的`static`内容先于子类处理完成。
`static`的内容包括`static variable`和`static initializer`，静态变量首先完成初始化，然后才会
调用`static initializer`，顺序很重要。

# 对象的初始化
对象初始化仍然遵循先父类后子类的顺序。
对象的初始化包括三部分并按照顺序完成：
- 对象属性初始化(定义时直接赋值的属性)
- 对象初始化器(instance initializer)
- 构造函数

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