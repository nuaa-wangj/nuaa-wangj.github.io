---
layout: post
title:  "Ubuntu中将Caps Lock键替换为Ctrl键"
category: [tech ]
tags: [Linux, ]
description: 将Caps Lock键替换为Ctrl键！
header-img: "img/blogs/blog-linux-ubuntu-capslock.jpg"
---



----
> 对于一些用户（如Emacs党）来说，Ctrl键是一个经常需要使用的键，但是由于其位于键盘的两个角落，使用起来不是十分顺手。大小写锁定键Caps Lock在实际使用中并不是十分常用，大小写切换的功能可以通过配合Shift键完成，下面给出将Caps Lock键替换为Ctrl键的方法。


### 废话不多说，直接给出简单的方法 ###

在`/etc/default/keyboard`文件中添加 

~~~
XKBOPTIONS="ctrl:nocaps"
~~~

重启后，就可以实现将Caps Lock键替换为Ctrl键。
有兴趣的话，可以看下面的内容。

### 其他方法 ###

#### 查看当前键盘布局 ####

~~~
$xmodmap -pke
----
...
keycode  59 = comma less comma less
keycode  60 = period greater period greater
keycode  61 = slash question slash question
keycode  62 = Shift_R NoSymbol Shift_R
keycode  63 = KP_Multiply KP_Multiply KP_Multiply KP_Multiply KP_Multiply KP_Multiply XF86ClearGrab
keycode  64 = Alt_L Meta_L Alt_L Meta_L
keycode  65 = space NoSymbol space
keycode  66 = Caps_Lock NoSymbol Caps_Lock
keycode  67 = F1 F1 F1 F1 F1 F1 XF86Switch_VT_1
...
~~~

*通常keycode 66 对应Caps_Lock键*

#### 使用自定义键盘布局 ####

创建一个键盘布局文件（如~/.Xmodmap）：

~~~
$ xmodmap -pke > ~/.Xmodmap
~~~

将其中的`keycode 66`对应的值改为`keycode  66 = Control_L NoSymbol Control_L`。然后使自定义文件生效：

~~~
$ xmodmap ~/.Xmodmap
~~~


#### 使用xmodmap命令修改 #####
下面修改可以将Caps Lock键修改为Control键，并且Shift+CapsLock为CapsLock。修改～/.Xmodmap文件内容为：

~~~
clear lock
clear control
add control = Caps_Lock Control_L Control_R
keycode 66 = Control_L Caps_Lock NoSymbol NoSymbol
~~~

然后执行：

~~~
$ xmodmap ~/.Xmodmap
~~~

或使用xmodmap说明文档中的例子,将Caps Lock键与Control键交换：

~~~
remove Lock = Caps_Lock
remove Control = Control_L
keysym Control_L = Caps_Lock
keysym Caps_Lock = Control_L
add Lock = Caps_Lock
add Control = Control_L
~~~

*参见`man xmodmap`*

#### 使用setxkbmap命令修改 ####
交换左Control和Caps Lock键

~~~
setxkbmap -option ctrl:swapcaps
~~~

将caps lock键改为ctrl键

~~~
setxkbmap -option ctrl:nocaps
~~~

**建议使用文章头给出的方法，简单有效。**

