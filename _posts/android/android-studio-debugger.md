title: Android Studio debugger
date: 2016-01-27 16:59:00
categories: android
tags: [android, studio, debugger]
---
Android Studio debugger使用指南。

<!-- more -->

# 普通指南
## 如何打断点
打断点的方法就是在编辑器的行号那一栏，右键即可设置断点，断点包括属性断点，
方法断点，行断点，顾名思义，就是把断点打到属性、方法、代码行上。断点设置
好之后可以直接点击"Debug"按钮，也可以运行起app之后再attach debugger到
app的进程上，两种方式均可。

## 停下来之后怎么调试
- step over: 逐行执行
- step into: 如果当前行有自定义的函数被调用，则跟进到该函数中（库函数会被略过）
- force step into: 如果当前行有库函数(如SDK函数)被调用，强制跟进到该函数中
- step out: 跳出当前执行的函数

# 进阶指南
`Ctrl+Shift+F8`
## 异常断点
在异常发生时停下来，异常可以是"被捕获的"、"未被捕获"的，比如在所有未被捕获的
`RuntimeException`处停下来。注意如果catch了exception，然后在catch中再次throw
该异常，则无法停下来，此时重新new一个RuntimeException并跑出来就可以停下来了。

## 属性断点
final的属性断点无法停下来，不知何故。

## 断点条件设置
可以给断点设置条件，比如for循环里，index等于3时才停下来。善用条件设置，
断点处能够使用的所有变量、方法、资源，都可以用来设置断点条件。

## 解释器(evaluator)
在断点处停下来之后，可以调出evaluator，此时断点处可以使用的所有资源都可以在
evaluator里面使用，此时的evaluator相当于一个REPL，非常方便，善用。

(over)