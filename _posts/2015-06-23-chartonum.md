---
layout: post
title: 数字与字符串的转化
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

在C/C++语言中没有专门的字符串变量，通常用字符数组来存放字符串。字符串是以“\0”作为结束符。C/C++提供了丰富的字符串处理函数。

1.字符串输出函数puts。

2.字符串输入函数gets。

3.字符串连接函数strcat。

4.字符串复制函数strcpy。

5.测字符串长度函数strlen。


##### 1.使用库函数将数字转换为字符串

解析：

C语言提供了几个标准库函数，可以将任意类型（整型、长整型、浮点型等）的数字转换为字符串：

1.**itoa()**：将整型值转换为字符串；

2.**ltoa()**：将长整型值转换为字符串；

3.**ultoa()**：将无符号长整型转换为字符串；

4.**gcvt()**：将浮点型数转换为字符串，取四舍五入；

5.**ecvt()**：将双精度浮点型值转换为字符串，转换结果中不包含十进制小数点。

6.**fcvt()**：指定位数为转换精度，其余同ecvt()。

还可以使用sprintf系列函数把数字转换成字符串，其比itoa()系列函数运行速度慢。

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	int num_int=435;
	double num_double=435.10f;
	char str_int[30];
	char str_double[30];
	char *str_int1=(char*)malloc(sizeof(10));

	itoa(num_int,str_int,10);        //把整数num_int转换成字符串str_int
	itoa(num_int,str_int1,10); 
	gcvt(num_double,8,str_double);   //把浮点数num_double转换成字符串str_double

	printf("str_int:%s\n",str_int);
	printf("str_int:%s\n",str_int1);
	printf("str_double:%s\n",str_double);
	system("pause");
}
```

程序运行结果：

![](/images/posts/Cpoint/64.png)

函数itoa中参数10表示按十进制类型进行转换，转换后的结果是“435”，如果按二进制类型进行转换，则结果为“1101110011”。

gcvt的参数8表示精确位数，这里得到的结果是“435.10001”。

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	double num_double=-4351.10f;
	int dec,sign;
	char *str=ecvt(num_double,10,&dec,&sign);
    printf( "str=%s   dec=%d   sign=%d ",str,dec,sign);
	system("pause");
}
```

程序运行结果：

![](/images/posts/Cpoint/65.png)

其中dec存储的小数点位置，sign代表转换的数的符号，0是正数，1是负数。


##### 2.不使用库函数将整数转换为字符串

解析：








