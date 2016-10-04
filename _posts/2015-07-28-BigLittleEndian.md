---
layout: post
title:  大端模式和小端模式
categories: C和C++基础
description: 大端模式和小端模式
keywords: C, 大端模式和小端模式
---

#### 大小端定义

**1，大端模式：内存的低位存数据的高位，内存的高地址存数据的低位。** 

**2，小端模式：内存的低地址存数据的低位，内存的高地址存数据的高位。**

![](/images/posts/C++/288.png)

例如，16bit宽的数0x1234在Little-endian模式CPU内存中的存放方式（假设从地址0x4000开始存放）为：

![](/images/posts/C++/289.png)

而在Big-endian模式CPU内存中的存放方式则为：

![](/images/posts/C++/290.png)

32bit宽的数0x12345678在Little-endian模式CPU内存中的存放方式（假设从地址0x4000开始存放）为：

![](/images/posts/C++/291.png)

而在Big-endian模式CPU内存中的存放方式则为：

![](/images/posts/C++/292.png)


编程判断大小端：

```cpp
#include <iostream>
using namespace std;

int main(){
	int a = 0x12345678;
	char *p = (char *)(&a);
	if(*p == 0x78){
		printf("xiao duan!\n");
	}else if(*p == 0x12){
		printf("da duan!\n");
	}else{
		printf("other!\n");
	}
	system("pause");
}
```

#### 大小端和共用体

1、请写一个C函数，若处理器是Big_endian的，则返回0；若是Little_endian的，则返回1。

```cpp
int checkCPU()
{ 
   union w{       //变量a的起始地址和变量b的起始地址是相同的
       int a;
       char b;
   }c;

   c.a = 1;
   return(c.b ==1);
}
```

2、求下面代码输出结果：

```cpp
#include <stdio.h>
#include <stdlib.h>

union Un{
	int a;
	char c[3];
};

int main(){
	Un A;
	A.c[0]=2;
	A.c[1]=13;
	A.c[2]=255;
    //共用体变量的地址和它的各成员的地址都是同一地址
	printf("%d  %0x\n",A.a,A.a);  
	system("pause");
}
```

![](/images/posts/C++/6.png)

程序解析：

```cpp
a: 02  0d  ff  随机值
c: 02  0d  ff

如果以小端去解析a： 随机值  ff  0d  02
如果以大端去解析a:  02     0d  ff  随机值
```

3、求下面代码输出结果：

```cpp
#include <stdio.h>
#include <stdlib.h>

union Un{
	int a;
	int b[3];
};

int main(){
	Un A;
	A.b[0]=0;
	A.b[1]=0;
	A.b[2]=0;
	A.a=6;
    //共用体变量的地址和它的各成员的地址都是同一地址
	printf("%d  %d  %d\n",A.b[0],A.b[1],A.b[2]);
	system("pause");
}
```

![](/images/posts/C++/7.png)

程序解析：

```cpp
由于机器是小端格式
a: 06  00  00  00
b: 06  00  00

所以a[0]=6  a[1]=0  a[2]=0
```

4、求下面代码输出结果：

```cpp
#include <stdio.h>
union{
	int i;
	char x[2];
}a;

int main(){
	a.x[0]=10;
	a.x[1]=1;
	printf("%d",a.i);
}

设机器为小端格式
```

程序解析：

```cpp
x:  0A  01 
a:  0A  01  00  00

以小端格式解析a，则a=0x0000010A 
也就是a=266
```

5、求下面代码输出结果：

```cpp
int main(){
	union{          //定义一个联合体
		int i;
		struct{     //在联合体中定义一个结构体
			char first;
			char second;
		}half;
	}number;
	number.i=0x4241;
	printf("%c%c\n",number.half.first,number.half.second);  //A、B
	number.half.first='a';
	number.half.second='b';
	printf("%x\n",number.i);   //6261
	system("pause");
}
```