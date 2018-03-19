---
layout: post
title:  "通过源码构建运行FlowDroid"
category: [tech ]
tags: [Android,]
description: Ubuntu中通过源代码构建FlowDroid并分析Android软件
header-img: "img/pages/template.jpg"
---


----

**新搭建了个人博客，文章全部转到[http://blog.datarepo.cn](http://blog.datarepo.cn)上更新。**

> 最近在研究Android软件的静态分析，[FlowDroid](https://blogs.uni-paderborn.de/sse/tools/flowdroid/)是一个高精度的Android程序污点分析工具，能够分析apk文件，生成软件调用图。下面给出了FlowDroid通过源码进行构建和运行的过程。

## 1. 安装相关软件 ##

#### 安装JDK 1.7，并移除JDK 1.6（FlowDroid不能在JDK 1.6下运行） ####
~~~ shell
sudo apt-get install openjdk-7-jdk openjdk-7-jre-headless 
sudo apt-get remove openjdk-6-jre openjdk-6-jre-headless  #（如果有的话）
~~~

#### 安装Eclipse、Git和相关插件 ####
~~~ shell
sudo apt-get install eclipse git eclipse-egit
~~~

## 2. 导入Eclipse #

下面介绍两种将FlowDroid导入Eclipse的方法。

#### 手动下载项目 ####

**创建文件夹**  
本地创建文件夹，用于存放项目代码。

~~~ shell
mkdir -p ~/workspace/flowdroid
cd ~/workspace/flowdroid
~~~

**在GitHub中clone项目到本地**

~~~ shell
git clone https://github.com/Sable/heros.git
git clone https://github.com/Sable/jasmin.git
git clone https://github.com/Sable/soot.git
git clone https://github.com/secure-software-engineering/soot-infoflow.git
git clone https://github.com/secure-software-engineering/soot-infoflow-android.git
~~~

**导入Eclipse**  
打开Eclipse选择 File -> Import -> General -> Existing Projects into Workspace，分别导入5个项目即可。

#### 通过psf项目集导入 ####

在Eclipse中使用URI导入团队项目集，能够一次性将5个项目同时导入到Eclipse中。5个项目包括：[`heros`](https://github.com/Sable/heros.git)、[`jasmin`](https://github.com/Sable/jasmin.git)、[`soot`](https://github.com/Sable/soot.git)、[`soot-infoflow`](https://github.com/secure-software-engineering/soot-infoflow.git)、[`soot-infoflow-android`](https://github.com/secure-software-engineering/soot-infoflow-android.git)。具体导入方法见下面截图：

**URI： [https://raw.githubusercontent.com/traceflight/Droidetect/master/develop/flowdroid.psf](https://raw.githubusercontent.com/traceflight/Droidetect/master/develop/flowdroid.psf)**

1. **打开 File -> Import -> Team -> Team Project Set**
![flowdroid-import](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-flowdroid-import.png)
![flowdroid-team-project-set](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-flowdroid-team-project-set.png)

2. **输入上面的URI -> 点击Finish**
![flowdroid-input-uri](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-flowdroid-input-URI.png)
![flowdroid-cloning](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-flowdroid-cloning.png)


## 3. 下载依赖文件 #

#### android.jar 文件 ####
对于Android开发人员来说，该文件很熟悉，在android sdk文件夹下，文件结构通常为sdk/platforms/android-22/android.jar。如果本地没有，可在官方下载[Android SDK](https://developer.android.com/sdk/index.html)，或者移步在我的项目中[下载](https://github.com/traceflight/Android-related-repo/tree/master/Android%20Jars)。

#### DroidBench数据集 ####
[DroidBench](https://github.com/secure-software-engineering/DroidBench)是用于评估Android软件污点分析工具有效性的开源测试集，由[EC SPRIDE Secure Software Engineering Group](http://sse.ec-spride.de/)创建并维护。FlowDroid中的测试代码部分使用到了DROIDBENCH环境变量，在分析自定义软件时不需要该变量。因此，该数据集不是运行FlowDroid的必须文件。如果需要，可在其Github项目[DroidBench](https://github.com/secure-software-engineering/DroidBench)中下载。

## 4. 配置项目 #

#### 环境变量 ####
FlowDroid在运行其测试程序时，需要用到两个环境变量：`ANDROID_JARS`和`DROIDBENCH`。
Ubuntu中可在~/.profile中设置相应的环境变量。使用`gedit ~/.profile`命令编辑，添加如下内容：

~~~ shell
export ANDROID_JARS=path/to/android.jar
export DROIDBENCH=path/to/droidbench
~~~

`ANDROID_JARS`指向Android SDK中某一版本的android.jar文件。
`DROIDBENCH`指向数据集所在的位置，本地中可在`soot-infoflow-android/test/soot/jimple/infoflow/android/test/droidBench`中找到，但其中的数据集不完整，无法完成所有的测试用例。完整的数据集在其官方项目[DroidBench](https://github.com/secure-software-engineering/DroidBench)中下载。

#### 其他配置 ####
  * **SLF4J文件重复问题**
  在项目`heros`和`soot-infoflow`两个项目中的classpath文件中均有`slf4j-simple-1.7.5.jar`。因此在项目编译时会提示SLF4J文件重复，解决方法是在`soot-infoflow`项目的`.classpath`文件中删除对应行`<classpathentry kind="lib" path="lib/slf4j-simple-1.7.5.jar"/>`。
  * **无法找到`EasyTaintWrapperSource.txt`**
  `soot-infoflow-anadroid`项目编译时提示找不到文件`EasyTaintWrapperSource.txt`。该文件可在项目`soot-infoflow`根目录下找到，复制到`soot-infoflow-anadroid`的根目录下即可。

## 5. 运行FlowDroid分析软件 #

#### 使用代码中的测试用例 ####
 `soot-infoflow-anadroid`项目提供了多个测试集，位于项目`test`文件夹下，分别为:droidBench数据集测试、insecureBank.apk测试、otherAPKs测试、sourceToSinks测试和xmlParser测试。使用方法为，右击对应的java文件，选择Run As -> JUnit Test。

#### 分析自定义文件 ####
 分析自定义文件使用`soot.jimple.infoflow.android.TestApps`中的`Test.java`文件。该文件的输入包括两个参数：apk-file和android-jar-directory。配置方法如下：

  * 右击Test.java文件，选择Run As -> Run Configurations...
  ![flowdroid-test-config](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-flowdroid-test-config.png)
  * 左侧选择Java Application里面的Test（如果打开Configurations后，左侧没有Test，可先执行Run As -> Java Application），右侧选择Arguments标签，里面写入两个参数，点击Apply、Run，即可得到分析结果。
  ![flowdroid-test-run](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-flowdroid-test-run.png)
  ![flowdroid-test-result](http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-flowdroid-test-result.png)
