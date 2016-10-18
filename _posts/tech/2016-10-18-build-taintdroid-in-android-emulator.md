---
layout: post
title:  "Android模拟器中构建TaintDroid"
category: [tech ]
tags: [Android,]
description: Android动态污点分析工具TaintDroid的编译运行
header-img: "img/pages/template.jpg"
---



----
>>下载编译运行TaintDroid

## 1. TaintDroid项目介绍

[TaintDroid](http://www.appanalysis.org/index.html)是由William Enck等人开发并实现的Android系统的实时隐私监控系统，常被用于Android软件动态分析。论文[TaintDroid: an information flow tracking system for real-time privacy monitoring on smartphones](http://www.appanalysis.org/tdroid10.pdf)发表在国际会议Usenix Conference on Operating Systems Design & Implementation。

TaintDroid通过修改Android源代码实现相关功能，其支持的最新系统版本为Android 4.3。

## 2. 构建TaintDroid

### Step 0: 安装相关软件

~~~ shell
$ sudo apt-get install curl git libswitch-perl gperf flex
~~~

上述软件为必须软件，若在实践过程中出现问题，自行Google即可。

### Step 1: 获取Android源代码

#### 安装Repo

Repo是谷歌用Python脚本写的调用git的一个脚本。主要是用来下载、管理Android项目的软件仓库。 更多关于Repo的信息，参见Google[开发](https://source.android.com/source/developing.html)网页。

**下载repo工具**

~~~ shell
$ mkdir ~/bin
$ PATH=~/bin:$PATH
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
~~~

**修改repo文件**

~~~ shell
$ gedit ~/bin/repo
//将 REPO_URL 一行替换成为：REPO_URL = 'https://gerrit-google.tuna.tsinghua.edu.cn/git-repo'
~~~

*前面两步也可简化为*

~~~ shell
$ mkdir ~/bin
$ PATH=~/bin:$PATH
$ wget -P ~/bin https://raw.githubusercontent.com/traceflight/Android-related-repo/master/Repo-Script/repo
$ chmod a+x ~/bin/repo
~~~

**配置Git**

~~~ shell
$ git config --global user.name "Your Name"
$ git config --global user.email "you@example.com"
~~~
#### 获得Android源代码

Android源代码托管的官方网站为：https://android.googlesource.com/platform/manifest。由于众所周知的原因，大陆无法访问，可使用清华大学开源软件镜像站代替使用。

**建立工作目录：**

~~~ shell
$ mkdir -p ~/tdroid/tdroid-4.3_r1
$ cd ~/tdroid/tdroid-4.3_r1
~~~

**初始化仓库，使用android 4.3**

~~~ shell
$ repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-4.3_r1
~~~

**同步源码树（所需时间较长）**

~~~ shell
$ repo sync
~~~

#### 编译源代码

源代码编译过程中可能出现各种问题，请Google相关问题。

~~~ shell
$ . build/envsetup.sh
$ lunch 1
$ make -j4
~~~

**运行模拟器**

~~~ shell
$ emulator
~~~

### Step 2: 获取TaintDroid源代码

#### 创建local_manifest.xml文件

~~~ shell
$ mkdir ~/tdroid/tdroid-4.3_r1/.repo/local_manifests/
$ cd ~/tdroid/tdroid-4.3_r1/.repo/local_manifests/
$ touch local_manifest.xml
$ gedit local_manifest.xml
~~~

复制下列内容到文件local_manifest.xml中

~~~
<manifest>
  <remote name="github" fetch="git://github.com"/>
  <remove-project name="platform/dalvik"/>
  <project path="dalvik" remote="github" name="TaintDroid/android_platform_dalvik" revision="taintdroid-4.3_r1"/>
  <remove-project name="platform/libcore"/>
  <project path="libcore" remote="github" name="TaintDroid/android_platform_libcore" revision="taintdroid-4.3_r1"/>
  <remove-project name="platform/frameworks/base"/>
  <project path="frameworks/base" remote="github" name="TaintDroid/android_platform_frameworks_base" revision="taintdroid-4.3_r1"/>
  <remove-project name="platform/frameworks/native"/>
  <project path="frameworks/native" remote="github" name="TaintDroid/android_platform_frameworks_native" revision="taintdroid-4.3_r1"/>
  <remove-project name="platform/frameworks/opt/telephony"/>
  <project path="frameworks/opt/telephony" remote="github" name="TaintDroid/android_platform_frameworks_opt_telephony" revision="taintdroid-4.3_r1"/>
  <remove-project name="platform/system/vold"/>
  <project path="system/vold" remote="github" name="TaintDroid/android_platform_system_vold" revision="taintdroid-4.3_r1"/>
  <remove-project name="platform/system/core"/>
  <project path="system/core" remote="github" name="TaintDroid/android_platform_system_core" revision="taintdroid-4.3_r1"/>
  <remove-project name="device/samsung/manta"/>
  <project path="device/samsung/manta" remote="github" name="TaintDroid/device_samsung_manta" revision="taintdroid-4.3_r1"/>
  <remove-project name="device/samsung/tuna"/>
  <project path="device/samsung/tuna" remote="github" name="TaintDroid/android_device_samsung_tuna" revision="taintdroid-4.3_r1"/>
  <project path="packages/apps/TaintDroidNotify" remote="github" name="TaintDroid/android_platform_packages_apps_TaintDroidNotify"
      revision="taintdroid-4.3_r1"/>
</manifest>
~~~

#### 获取源代码

~~~ shell
$ cd ~/tdroid/tdroid-4.3_r1
$ repo sync
$ repo forall dalvik libcore frameworks/base frameworks/native frameworks/opt/telephony system/vold system/core device/samsung/manta device/samsung/tuna \
 packages/apps/TaintDroidNotify -c 'git checkout -b taintdroid-4.3_r1 --track github/taintdroid-4.3_r1 && git pull'
~~~

### Step 3: 构建TaintDroid

#### 创建buildspec.mk文件，并设置

~~~ shell
$ cd ~/tdroid/tdroid-4.3_r1
$ touch buildspec.mk
$ gedit buildspec.mk
~~~

复制如下内容到buildspec.mk中

~~~
# Enable core taint tracking logic (always add this)
WITH_TAINT_TRACKING := true

# Enable taint tracking for ODEX files (always add this)
WITH_TAINT_ODEX := true

# Enable taint tracking in the "fast" (aka ASM) interpreter (recommended)
WITH_TAINT_FAST := true

# Enable additional output for tracking JNI usage (not recommended)
#TAINT_JNI_LOG := true

# Enable byte-granularity tracking for IPC parcels
WITH_TAINT_BYTE_PARCEL := true
~~~

#### 修改core.mk文件

~~~ shell
$ gedit ~/tdroid/tdroid-4.3_r1/build/target/product/core.mk
~~~

在第一个`PRODUCT_PACKAGES`字段最后添加

~~~
\
TaintDroidNotify
~~~

最后得到的`PRODUCT_PACKAGES`形如：

~~~
PRODUCT_PACKAGES += \
                    BasicDreams \
                    ...
                    voip-common \
                    TaintDroidNotify
~~~

#### 构建TaintDroid（Android模拟器中）

~~~ shell
$ cd ~/tdroid/tdroid-4.3_r1
$ . bulid/envsetup.sh
$ lunch full-eng      //Android emulator
$ make clean
$ make -j4
~~~

### Step 4: 获得YAFFS2 XATTR内核支持

~~~ shell
$ cd ~/tdroid
$ wget http://www.appanalysis.org/files/kernel-goldfish-xattr-2.6.29.zip //此处官方有误
$ unzip kernel-goldfish-xattr-2.6.29.zip
~~~

### Step 5: 生成sdcard.img（可选）

很多应用安装时需要有SD卡支持，按照官方教程没有给出sdcard.img的生成方法。需要说明的是，不执行本步操作，正常情况下不影响系统和应用的执行。

生成sdcard.img需要使用Android的SDK中的工具`mksdcard`，该工具位于sdk根目录的tools文件夹中。创建方法如下

~~~ shell
$ cd path_to_android_sdk/tools
$ ./mksdcard 1024M sdcard.img
~~~

将sdcard.img文件放置在指定文件夹下

~~~ shell
$ cp path_to_android_sdk/tools/sdcard.img ~/tdroid/tdroid_4.3_r1/out/target/product/generic/
~~~

### Step 6: 运行并测试TaintDroid

在运行TaintDroid之前，强烈建议先将构建的生成结果备份一下，这样可以快速的将TaintDroid恢复到刚刚构建的状态。需要备份的文件在~/tdroid/tdroid_4.3_r1/out/target/product/generic文件夹中，建议将其中除obj文件夹和symbols文件夹外的其他文件备份，并放置在其他文件夹中（如~/tdroid文件夹）。

#### 运行TaintDroid
~~~ shell
$ cd ~/tdroid
$ emulator -kernel kernel-goldfish-xattr-2.6.29
~~~

运行结果如下图

![taintdroid-after-build](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-taintdroid-after-build.png)

#### 测试

向Android模拟器中安装软件，本文安装百度手机助手。使用Android的SDK中的adb工具：

~~~ shell
$ cd path_to_android_sdk/platform-tools
$ ./adb install path/to/apk/file
~~~

启动TaintDroid

![taintdroid-start](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-taintdroid-start.png)

启动百度手机助手

![taintdroid-openbaidu](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-taintdroid-openbaidu.png)

可以看到TaintDroid的通知

![taintdroid-notify](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-taintdroid-notify.png)

点击其中的一个通知，可以看到详细内容

![taintdroid-notify-detail](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-taintdroid-notify-detail.png)



