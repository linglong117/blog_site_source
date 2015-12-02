title: Android am命令
date: 2015-12-02 14:42:13
categories: android
tags: [android, am]
---

`adb shell am`的用法。

<!-- more -->

`am(activity manager)`命令是android增加的一个linux shell command，
该命令用于才命令行操作activity manager，相关的命令还有`pm(package manager)`，
`wm(window manager)`等。

# Usage
`usage: am [subcommand] [options]`

其中子命令`start`最为常用，`am start`用于启动一个activity，用法是： `am start [options] <intent>`
intent有很多形式，可以指定action，category，uri，package name等等，如打开指定的网址：
`adb shell am start http://www.baidu.com`

(待续)