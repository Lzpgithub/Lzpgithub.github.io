---
layout: post
title: 编程基础-编程中常用的函数
categories: C和C++基础
description: 编程中常用的函数
keywords: 编程中常用的函数
---

#### 输入输出

1.gets和scanf

scanf输入字符串当字符串中含有空格的时候，空格后面的部分将不会被读入；而用gets则可以；比如一定义变量name:

```cpp
scanf("%s",name);
gets(name);
```

当你从键盘输入:string question时,两者的name变量的值分别为：1、string;2、string question;

2.puts

```cpp
#include <iostream>

using namespace std;

int main(){
	char name[20];
	gets(name);
	puts(name);
	system("pause");
}
```

![](/images/posts/C++/186.png)

#### 数值转换为char*

1.itoa，ltoa，ultoa：int转char*

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	//itoa() ltoa() ultoa() 参数和用法都一样
	int num_int=435;
	char str_int1[30];
	char *str_int2=(char*)malloc(sizeof(10));

	itoa(num_int,str_int1,10);        //把整数num_int转换成字符串str_int
	itoa(num_int,str_int2,10); 
	
	printf("str_int:%s\n",str_int1);
	printf("str_int:%s\n",str_int2);

	long long num1=123;
	char str_num1[30];
	ltoa(num1,str_num1,10);
	printf("str_int:%s\n",str_num1);

	system("pause");
}
```

![](/images/posts/C++/187.png)

2.gcvt：float，double转char*

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	float num_float=12.345;
	double num_double=435.14354656;
	char str_float[30];
	char str_double[30];

	gcvt(num_float,4,str_float);   //4和8是控制精度的
	gcvt(num_double,8,str_double); 

	printf("str_float:%s\n",str_float);
	printf("str_double:%s\n",str_double);

	system("pause");
}
```

3.sprintf函数：

位于头文件：stdio.h

函数的原型：int sprintf(char *buffer, const char *format, [argument]...);

参数列表

buffer：char型指针，指向将要写入的字符串的缓冲区;

format：格式化字符串;

[argument]...：可选参数，可以是任何类型的数据；

返回值：返回写入buffer的字符数，出错则返回-1。

```cpp
char str1[10];               //整型
int num1=123;
sprintf(str1,"%d",num1);

char str1[20];               //浮点型
float f1=3.1415926;
sprintf(str1,"%f",f1);

char str1[20];               //连接两个字符串
char *who = "I";
char *whom = "CSDN";
sprintf(str1,"%s love %s.",who,whom);   //产生:"I love CSDN."
```


![](/images/posts/C++/188.png)

#### char*转数值

atoi()，atol()，atof()：

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	int num_int;
	double num_double;
	char str_int[30]="435";
	char str_double[30]="436.55";

	num_int=atoi(str_int);           //转换为整型值
	num_double=atof(str_double);     //转换为浮点型值

	printf("num_int:%d\n",num_int);
	printf("num_double:%f\n",num_double);
	
	system("pause");
}
```

![](/images/posts/C++/189.png)

#### printf函数

%d或%i：有符号十进制

%o：八进制

%0x或%0X：十六进制

%f：单精度或双精度

%c：单个字符

%s：字符串

%p：指针

-：结果左对齐，右边填空格
