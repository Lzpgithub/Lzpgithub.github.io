---
layout: post
title: 预处理
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

预处理是指通过预处理的内建功能对一个资源进行等价替换，最常见的预处理有：**文件包含(#include)、条件编译(#ifdef、#else)、布局控制(#progma)以及宏替换(#define)**。

#### #ifdef、#else、#endif指示符

**\#ifdef**指示符常被用来判断一个预处理器常量是否已被定义，以便有条件地包含程序代码。**\#ifndef**与**\#ifdef**作用相同，只是判断方式相反。

##### 1.分析程序的输出结果--预处理的使用

```cpp
#include <stdio.h>
#include <stdlib.h>

#define DEBUG

int main(){
	int i=0;
	char c;
	while(1){
		i++;
		c=getchar();
		if(c!='\n'){
			getchar();
		}

		if(c=='q'||c=='Q'){
			#ifdef DEBUG
				printf("we got:%c,about to exit.\n",c);
			#endif
			break;
		}else{
			printf("i=%d",i);
			#ifdef DEBUG
				printf(",we got:%c",c);
			#endif
			printf("\n");
		}
	}
	system("pause");
}
```

程序首先定义了名为DEBUG的预处理器常量，然后用\#ifdef判断DEBUG是否被定义，如果定义了，就进行printf输出信息。

输出结果如下：

![](/images/posts/C++/33.png)

#### 宏定义

良好的宏定义对于写好C语言十分重要，使用宏定义可以防止出错，提高代码的可移植性和可读性。

##### 2.用#define实现宏，并求最大值和最小值

实现代码：

```cpp
#define MAX(x,y) (((x)>(y))?(x):(y))
#define MIN(x,y) (((x)<(y))?(x):(y))
```

##### 3.分析代码写结果--宏定义的使用

```cpp
#include <stdio.h>
#include <stdlib.h>

#define SOR(x) (x*x)

int main(){
	int a,b=3;
	a=SOR(b+2);
	printf("a = %d\n",a);  //输出11
	system("pause");
}
```

由于宏定义展开是在预处理时期，也就是在便于之前。此时b并没有被赋值，这时的b只是一个符号。因此，其实宏定义被展开成：

```cpp
a=(b+2*b+2);
```

为了达到平方的目的，可以将SOR(x)改成如下定义：

```cpp
#define SOR(x) ((x)*(x))
```

##### 4.分析代码写结果--宏参数的连接

```cpp
#include <stdio.h>
#include <stdlib.h>

#define STR(s)     #s
#define CONH(a,b)  (int)(a##e##b)
#define CONS(a,b)  (int)(a##2##b)

int main(){
	printf(STR(vck));           //输出vck
	printf("\n");
	printf("%d\n",CONH(2,3));   //输出2000  2e3
	printf("%d\n",CONS(2,3));   //输出223
	system("pause");
}
```

在本程序中，**使用\#把宏参数变为一个字符串，用\##把两个宏参数贴合在一起**。


##### 5.用宏定义得到一个字的高位和低位字节

```cpp
#define WORD_LO(xxx) ((byte)((word)(xxx)&255))
#define WORD_HI(xxx) ((byte)((word)(xxx)>>8))
```

一个字由2个字节组成，xxx先转换成word，在转换成byte。

##### 6.用宏定义得到一个数组所含的元素个数

```cpp
#define ARR_SIZE(a) (sizeof((a))/sizeof((a[0])))
```

下面的代码的作用是防止多重包含drawcoordinate.h头文件：

```cpp
//drawcoordinate.h
//如果没定义DRAWCOORDINATE_H，则执行以下代码
#ifndef DRAWCOORDINATE_H   
//在这里定义DRAWCOORDINATE_H，下次重新包含这个文件时
//因为已经定义过DRAWCOORDINATE_H，就不会再次执行，确保该文件只包含一次
#define DRAWCOORDINATE_H   
. 
.
.
#endif
```

##### 7.用预处理指令#define声明一个常数，用以表明1年终有多少秒（忽略闰年问题）

```cpp
#define SECONDS_PER_YEAR (60*60*24*365)UL
```

