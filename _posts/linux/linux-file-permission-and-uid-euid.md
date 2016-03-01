title: Linux文件权限标志位
date: 2016-03-01 14:05:50
categories: linux/unix
tags: [suid, sgid, sticky bit]
---

Linux系统文件权限标志位。
<!-- more -->

# 文件权限格式

Linux系统文件权限用下图所示的格式来表示，

```c
/*
9 876 543 210
- rwx rwx rwx
- --- --- ---
|  |   |   |
|  |   |   +-> permission bits for others
|  |   +-----> permission bits for group
|  +---------> permission bits for owner
+------------> file type bit
*/
```

`876`, `543`, `210`分别表示**others**, **group**, **owner**
所具有的权限(read, write, execute)，如果没有对应的权限，则用`-`表示，
如`- rw- r-- ---`表示`owner`具有读写权限，跟`owner`同组的用户具有读权限，
其他用户则没有任何操作该文件的权限。

另外还有另一种表示方法。`read`, `write`, `execute`权限分别用数字4，2，1来表示，
没有权限则用数字0表示，然后相加就得到一个数字，每一组权限都有一个数字对应，
如刚才的权限`- rw- r-- ---`用数字格式表示为`- 6 4 0`。

# 文件类型
最左边的位置(即9那个位置)用来表示文件类型，可能的值为：

- `-`：普通文件
- `b`：块设备文件(block)
- `c`：字符设备文件(char)
- `d`：目录文件(directory)
- `l`：符号链接文件(link)
- `p`：命名管道文件(pipe)
- `s`：socket文件

# suid, sgid, sticky bit
对于每组的`execute`权限位又有特殊的规定，这就是suid(set uid), sgid(set gid)和
sticky bit。

## suid
对于**owner**的`execute`权限位，还可以设置`suid`标志，用`s`来表示，如果该位置本来
就有可执行权限位，即`x`，则`s`和`x`叠加后用大写的`S`来表示。

## sgid
类似与suid，对于**group**的`execute`权限位，还可以设置`guid`标志，也用`s`来表示，
同样的，如果该位置本来就有可执行权限位，即`x`，则`s`和`x`叠加后用大写的`S`来表示。

## sticky bit
不同于suid, guid，对于**others**的`execute`权限位，则可以设置`sticky bit`标志，
用`t`来表示，如果该位置本来就有可执行权限位，即`x`，则`t`和`x`叠加后用大写的`T`来表示。

sticky bit只对目录起作用，如果一个目录设置了sticky bit，则该目录下的文件只能被
该文件的owner或者root删除，其他用户即使有删除权限也无法删除该文件。

例如，`/tmp`目录，它的权限为`d rwx rwx rwt`，该目录中的文件(或目录)只能被owner
或root删除，这样大家都可以把自己的临时文件往该目录里面放，但是你的文件别人是无法
删除的。

注意：`suid`, `sgid`只对**文件**起作用，而sticky bit只对**目录**起作用。

# suid, sgid标志位的作用
要想说明白suid, guid的作用，首先要弄明白uid, gid以及euid, guid的作用。

uid, gid在用户登陆的时候就确定了，uid用来唯一标识一个用户，guid也是固定好了的。
但是euid, egid又是什么呢？

考虑`/etc/passwd`文件以及`passwd`命令的使用场景，它们的文件权限如下，

- `/etc/passwd`: `- rw- r-- r--`
- `/usr/bin/passwd`: `- rws r-x r-x`

passwd文件是如此重要，以至于该文件
除了owner(即root)，其他人都是只能读，不能写，但是任何用户都允许修改自己的信息，即
修改这个文件，那怎么办呢，linux系统为大家提供了一个passwd命令来修改这个文件，而且
任何人都可以执行passwd命令，但是执行这个命令的时候，passwd文件仍然无法修改，因为
此时的用户仍然不是passwd文件的owner，怎么办呢？解决办法就是在执行passwd命令的时候，
把用户临时改成passwd文件的owner，这样就相当于其他用户以owner的身份来修改/etc/passwd，
命令执行完之后再恢复为原来的用户id。

此时就要用到一个临时的用户id，用它来记录passwd命令运行时的**有效用户id**，也就是
euid(effective uid，相当于运行时uid)，linux文件权限系统核对用户身份时使用euid而
不是uid(也叫做real uid, ruid)。

哪些命令执行的时候可以临时修改用户id呢？那就是设置了`suid`标志位的那些可执行文件，
可见，`suid`只对文件有效，而且是可执行文件，所以该标志位放在`execute`权限位的位置上。
`sgid`的原理是类似的。

总结一下，

- `suid`: set uid，具有该标志位的可执行文件在运行时，会将用户的`euid`设置为该文件的`uid`
- `sgid`: set gid，具有该标志位的可执行文件在运行时，会将用户的`egid`设置为该文件的`gid`

需要注意的是，`uid`, `euid`是实实在在的数字，而`suid`只是文件的一个标志位，`gid`也是类似的。

(over)