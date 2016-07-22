---
layout: post
title: C语言格式输出函数printf()详解
categories: C和C++基础
description: printf()
keywords: printf()
---

printf函数称为格式输出函数，其关键字最末一个字母f即为“格式”(format)之意。其功能是按用户指定的格式，把指定的数据显示到显示器屏幕上。

rintf函数是一个标准库函数，它的函数原型在头文件“stdio.h”中。但作为一个特例，不要求在使用 printf函数之前必须包含stdio.h文件。printf函数调用的一般形式为：

**printf(“格式控制字符串”, 输出表列)**

**1.类型:**

![](/images/posts/C++/91.png)

还有：

**%i：**  有符号十进制数（与％d相同）；

**%p：**　指针；

**2.标志字符为 -、+、# 和空格四种：**

![](/images/posts/C++/92.png)


**3.输出最小宽度：**

用十进制整数来表示输出的最少位数。若实际位数多于定义的宽度，则按实际位数输出，若实际位数少于定义的宽度则补以空格或0。

**4.精度：**

精度格式符以“.”开头，后跟十进制整数。本项的意义是：如果输出数字，则表示小数的位数；如果输出的是字符，则表示输出字符的个数；若实际位数大于所定义的精度数，则截去超过的部分。

**5.长度：**

长度格式符为h、l两种，h表示按短整型量输出，l表示按长整型量输出。

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(void){
   int a=15;
   long float b=123.1234567;
   double c=12345678.1234567;
   char d='p';
   printf("a=%d\n", a);
   printf("a(%%d)=%d, a(%%5d)=%5d, a(%%o)=%o, a(%%x)=%x\n\n",a,a,a,a);        //%%可以输出%
   printf("a=%f\n", b);
   printf("b(%%f)=%f, b(%%lf)=%lf, b(%%5.4lf)=%5.4lf, b(%%e)=%e\n\n",b,b,b,b);
   printf("c=%f\n", c);
   printf("c(%%lf)=%lf, c(%%f)=%f, c(%%8.4lf)=%8.4lf\n\n",c,c,c);
   printf("d=%c\n", d);
   printf("d(%%c)=%c, d(%%8c)=%8c\n",d,d);
   system("pause");
}
```

程序运行结果：

![](/images/posts/C++/93.png)


```cpp
#include <stdio.h>   
#include <stdlib.h>
#include <string.h>

int main()   
{   
    char c, s[20];   
    int a=1234;  
    float f=3.141592653589;   
    double x=0.12345678912345678;   
    strcpy(s, "Hello,World");   
    c='\x41';   
    printf("a=%d\n", a);        //按照十进制整数格式输出，显示 a=1234  
    printf("a=%d%%\n", a);      //输出%号 结果 a=1234%  
    printf("a=%6d\n", a);       //输出6位十进制整数 左边补空格，显示 a= 1234  
    printf("a=%06d\n", a);      //输出6位十进制整数 左边补0，显示 a=001234  
    printf("a=%2d\n", a);       //a超过2位，按实际输出 a=1234  
    printf("a=%-6d\n", a);      //输出6位十进制整数 右边补空格，显示 a=1234  
    printf("f=%f\n", f);        //浮点数有效数字是7位，结果 f=3.141593  
    printf("f=6.4f\n", f);      //输出6列，小数点后4位，结果 f=3.1416  
    printf("x=%lf\n", x);       //输出长浮点数 x=0.123457  
    printf("x=%18.16lf\n", x);  //输出18列，小数点后16位，x=0.1234567891234567  
    printf("c=%c\n", c);        //输出字符 c=A  
    printf("c=%x\n", c);        //以十六进制输出字符的ASCII码 c=41  
    printf("s[]=%s\n", s);      //输出数组字符串s[]=Hello,World  
    printf("s[]=%6.9s\n", s);   //输出最多9个字符的字符串 s[]=Hello,Wor  
    system("pause");  
}  
```

程序运行结果：

![](/images/posts/C++/94.png)


```cpp
#include <stdio.h>
#include <stdlib.h>

int main()
{ 
	int a = -1;
    printf("%d, %o\n",a,a);
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/95.png)

程序解析：-1在内存单元中（以补码形式存放）为(11111111 11111111 11111111 11111111)2，转换为八进制数为(37777777777)8


**使用printf函数时要注意一个问题，那就是输出表列中的求值顺序。不同的编译系统不一定相同，可以从左到右，也可从右到左。但是求值顺序虽是自右至左，但是输出顺序还是从左至右，因此得到的结果是上述输出结果。**。


```cpp
#include <stdio.h>
#include <stdlib.h>

int main(void){
    int i=8;
    printf("The raw value: i=%d\n",i);
    printf("++i=%d \n++i=%d \n--i=%d \n--i=%d\n",++i,++i,--i,--i);  
    system("pause");
}
```

Turbo C环境下运行：

The raw value: i=8

++i=8

++i=7

--i=6

--i=7

计算顺序是从右到左。

在vs2010环境下运行：

The raw value: i=8

++i=8

++i=8

--i=8

--i=8

vs2010计算过程比较复杂，输出结果是不可预测的。这是因为这个在C标准里面属于未定义的行为是由编译器压栈出栈所决定的，不属于C语言的东西，不同的编译器会产生不同的结果。



