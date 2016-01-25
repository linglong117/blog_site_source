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

## subcommand: start, startservice, broadcast
用法： `am [start|startservice|broadcast] [options] <intent>`

- start: 启动一个activity，如`adb shell am start http://www.baidu.com`
- startservice: 启动一个service
- broadcast: 发送一个broadcast

`<intent>`的形式如下：

```
<INTENT> specifications include these flags and arguments:
    [-a <ACTION>]       # 定义action
    [-d <DATA_URI>]     # 定义data uri
    [-t <MIME_TYPE>]    # 定义多媒体类型
    [-c <CATEGORY> [-c <CATEGORY>] ...]                 # 定义category
    [-e|--es <EXTRA_KEY> <EXTRA_STRING_VALUE> ...]      # 增加String类型的extra信息
    [--esn <EXTRA_KEY> ...]                             # 增加extra key
    [--ez <EXTRA_KEY> <EXTRA_BOOLEAN_VALUE> ...]        # 增加boolean类型的extra信息
    [--ei <EXTRA_KEY> <EXTRA_INT_VALUE> ...]            # 增加int类型的extra信息
    [--el <EXTRA_KEY> <EXTRA_LONG_VALUE> ...]           # 增加long类型的extra信息
    [--ef <EXTRA_KEY> <EXTRA_FLOAT_VALUE> ...]          # 增加float类型的extra信息
    [--eu <EXTRA_KEY> <EXTRA_URI_VALUE> ...]            # 增加extra URI
    [--ecn <EXTRA_KEY> <EXTRA_COMPONENT_NAME_VALUE>]    # 增加extra comonent
    [--eia <EXTRA_KEY> <EXTRA_INT_VALUE>[,<EXTRA_INT_VALUE...]]         # 批量增加int类型的extra信息
    [--ela <EXTRA_KEY> <EXTRA_LONG_VALUE>[,<EXTRA_LONG_VALUE...]]       # 批量增加long类型的extra信息
    [--efa <EXTRA_KEY> <EXTRA_FLOAT_VALUE>[,<EXTRA_FLOAT_VALUE...]]     # 批量增加float类型的extra信息
    [--esa <EXTRA_KEY> <EXTRA_STRING_VALUE>[,<EXTRA_STRING_VALUE...]]   # 批量增加String类型的extra信息
        (to embed a comma into a string escape it using "\,")           # 如果字符串中包含逗号，需要用"\,"进行转义
    [-n <COMPONENT>]    # 定义component信息
    [-p <PACKAGE>]      # 定义package信息
    [-f <FLAGS>]        # flags信息
    [--grant-read-uri-permission]           # 增加权限
    [--grant-write-uri-permission]          # 增加权限
    [--grant-persistable-uri-permission]    # 增加权限
    [--grant-prefix-uri-permission]         # 增加权限
    [--debug-log-resolution]
    [--exclude-stopped-packages]            # 排除stop状态的packages，系统默认
    [--include-stopped-packages]            # 包括stop状态的packages，默认intent不包括stop状态的packages
    [--activity-brought-to-front]
    [--activity-clear-top]
    [--activity-clear-when-task-reset]
    [--activity-exclude-from-recents]
    [--activity-launched-from-history]
    [--activity-multiple-task]
    [--activity-no-animation]
    [--activity-no-history]
    [--activity-no-user-action]
    [--activity-previous-is-top]
    [--activity-reorder-to-front]
    [--activity-reset-task-if-needed]
    [--activity-single-top]
    [--activity-clear-task]
    [--activity-task-on-home]
    [--receiver-registered-only]
    [--receiver-replace-pending]
    [--selector]
    [<URI> | <PACKAGE> | <COMPONENT>]
```

(待续)