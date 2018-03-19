---
layout: post
title:  "C/C++局部变量在栈区的存储情况分析"
category: tech
tags: [memory,stack,]
description:
---


---

**新搭建了个人博客，文章全部转到[http://blog.datarepo.cn](http://blog.datarepo.cn)上更新。**

>  最近看到同学书上写了个例子，关于strcpcy导致的内存覆盖问题，自己实验后发现与书上所讲结果不同。之后跟同学一起深入分析了一段程序，分析结果如下文。

## 考虑如下代码的运行结果（在vc++编译器中运行） ##

~~~ c++
#include <iostream>
using namespace std;
int main()
{
	char s[]="abcdefghijklmn";
	char d[]="zy";
	strcpy(d,s);
	cout<<"s = "<<s<<endl;
	cout<<"d = "<<d<<endl;
	system("pause");
	return 1;
}
~~~

一般看到这个题，最先的反应是，字符串s是源串，肯定不会变...但是，神奇的是s真的变了！我们来看看为什么～

## 变量在内存中的存储情况 ##

一个由C/C++编译的程序占用的内存分为以下几个部分

* **栈区** — 由编译器自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。在一个进程中，位于用户虚拟地址空间顶部的是用户栈，编译器用它来实现函数的调用。

* **堆区** — 一般由程序员分配释放， 若程序员不释放，程序结束时可能由OS回收 。注意它与数据结构中的堆是两回事。

* **全局/静态存储区** — 全局变量和静态变量被分配到同一块内存中，在以前的 C 语言中，全局变量又分为初始化的和未初始化的（初始化的全局变量和静态变量在一块区域，未初始化的全局变量与静态变量在相邻的另一块区域，同时未被初始化的对象存储区可以通过 void* 来访问和操纵，程序结束后由系统自行释放），在 C++ 里面没有这个区分了，他们共同占用同一块内存区。

* **常量存储区** — 这是一块比较特殊的存储区，他们里面存放的是常量，不允许修改（当然，你要通过非正当手段也可以修改，而且方法很多）

*上面的文字摘抄自网络，具体的区别可以自行谷歌。这里只分析栈区的内存情况，其他的可以自己研究。*

## 开始实例分析 ##

现在看如下代码

~~~ c++
#include <iostream>
using namespace std;
int main()
{
	int a=290,b=2;
	char c='w',d='x';
	char e[] = "abcz";
	char f[] = "def";
	return 1;
}
~~~

*以上变量均存放于系统栈区，存放于堆区、全局区等的情况有所不同。*

其中各变量在栈中的存储情况**示意图**如下所示：

<center>
    <p><img src="http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-memory-example.png" align="center"></p>
</center>

说明：

1. 图中内存为栈空间；

2. 图中一小格为一个字节；

3. 内存中存储为4字节对齐，不满4字节的按照4字节处理；

5. 申请变量的内存地址为从高到低；

6. 深色部分为编译器自动分配的变量间间隔，其大小取决于编译器。在我的实验中，windows下vs++编译器分配的间隔为8个字节，linux下gcc间隔为12字节；

7. 编译时可以确定其大小的变量（如 int， char等）在内存中分布为：变量高位与间隔后字单元高位对齐，如图中的变量a=290及c=’w’等；

8. 对于字符串类型的变量在内存中分布为：变量低位与字单元（可能为多个字单元，与变量对齐后大小有关）低位对齐，数组内容分布为从低到高分布，如字符数组e和f。

9. 指针位置为当前变量最低位地址。

指针&e与指针&f的距离（字节数）可如下计算：sizeof(f)%4? (sizeof(f)/4+1)*4+8 : sizeof(f)+8

*(sizeof()函数返回值包括字符数组结尾的’\0’,上式中的数字8为间隔字节数，根据编译器不同而不同)*

strcpy函数的执行逻辑，如下所示：

~~~ c++
_TCHAR *_tcscpy(_TCHAR *pDst, const _TCHAR *pSrc)
{
    _TCHAR * r = pDst;

    while ((*pDst++ = *pSrc++) != '\0')
        continue;

    return r;
}
~~~

在进行字符串复制时，仅检查源字符串的结尾，而不对目的字符串的长度进行检查。

## 重新分析文章开头的例子 ##

再看一下代码

~~~ c++
#include <iostream>
using namespace std;
int main()
{
	char s[]="abcdefghijklmn";
	char d[]="zy";
	strcpy(d,s);
	cout<<"s = "<<s<<endl;
	cout<<"d = "<<d<<endl;
	system("pause");
	return 1;
}
~~~

根据上面分析，可以将内存中的情况画出如下所示的图：

<center>
    <p><img src="http://7xsbrq.com1.z0.glb.clouddn.com/img/blogs/blog-memory-analysis.png" align="center"></p>
</center>

可以发现，字符串s被d的内容覆盖掉了，可以看到字符串d会被截断，因此，字符串（以’\0’结尾）输出结果为

~~~~~
	s = mn
	d = abcdefghijklmn
~~~~~

*注：Linux下gcc编译运行结果不同，原因为gcc为变量间分配的间隔为12字节，因此可增加s字符串的长度，实现类似的效果，如char s[]="abcdefghijklmnopqr"，则输出s为qr。*

## 结论 ##

MSDN上给出的建议是：

 To avoid overflows, the size of the array pointed by destination shall be long enough to contain the same C string as source (including the terminating null character), and should not overlap in memory with source. Because strcpy does not check for sufficient space in strDestination before it copies strSource, it is a potential cause of buffer overruns. Therefore, we recommend that you use strcpy_s instead. -- MSDN

即在使用strcpy时需要对目标字符串的长度进行事先判定，使其至少等于源字符串，或者改用strcpy_s函数。

* 以上内容为我自己实验结果所得，如有疑问可自行实验后告知，谢谢～
