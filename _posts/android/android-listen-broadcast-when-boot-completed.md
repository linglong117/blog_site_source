title: Android开机自启动应用
date: 2016-01-25 19:00:52
categories: android
tags: [android, broadcast, boot]
---

Android开机自启动应用需要注意的一些坑。

<!-- more -->

开机自启动应用有些需要注意的地方，如果不注意很容易掉到坑里去。

# 权限
必须声明此权限
`<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />`

# 定义receiver
```xml
<receiver android:name=".BootReceiver"
          android:enabled="true">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>
```
其中，`android:enable="true"`这个属性必不可少。

# 必须手动启动一次
任何app安装之后都是处于"stopped"状态，此时，它是不会收到`BOOT_COMPLETED`消息的，
进入设置查看该app状态可以发现，`force stop`是不可用的，所以需要从Launcher启动该
app，之后它就不再是stopped状态，重启手机就可以收到`BOOT_COMPLETED`消息了。

(over)