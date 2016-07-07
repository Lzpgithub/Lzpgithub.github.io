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

![](/images/posts/C++/64.png)

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

![](/images/posts/C++/65.png)

其中dec存储的小数点位置，sign代表转换的数的符号，0是正数，1是负数。


##### 2.不使用库函数将整数转换为字符串

解析：

如果不使用itoa或sprintf等库函数，可以通过把整数的各位上的数字加“0”转换成char类型并存到数组中，但要注意，需要采用字符串逆序的方法。

```cpp
#include <iostream>

using namespace std;

void int2str(int n,char *&str){
	str=(char*)malloc(10*sizeof(char));
	char buf[10]="";
	int i=0;
	int len=0;
	int temp=n<0?-n:n;   //temp为n的绝对值

	if(str==NULL){       //判断为空则返回
		return;
	}
	while(temp){
		buf[i++]=(temp%10)+'0';   //把temp的每一位上的数存入buf
		temp=temp/10;
	}

	len=n<0?++i:i;       //如果n是负数 则多需要一位来存储负号
	str[i]=0;            //末尾是结束符0
	for(int j=i-1;j>=0;j--){
		str[j]=buf[len-j-1];
	}
	if(n<0)
		str[0]='-';
}

int main(){
	char *str;
	int2str(13454,str);
	cout<<str<<endl;
	
	system("pause");
}
```


##### 3.使用库函数将字符串转换为数字

**atof()**：将字符串转换为双精度浮点型。

**atoi()**：将字符串转换为整型值。

**atol()**：将字符串转换为长整型值。

**strtod()**：将字符串转换为双精度浮点型，并报告不能被转换的所有剩余数字。

**strtol()**：将字符串转换为长整型值，并报告不能被转换的所有剩余数字。

**strtoul()**：将字符串转换为无符号长整型值，并报告不能被转换的所有剩余数字。


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

##### 4.不使用库函数将字符串转换为数字

```cpp
#include <iostream>

using namespace std;

int str2int(const char *str){
	int temp=0;
	const char *ptr=str;              //ptr保存str字符串开头
	if(*str=='-'||*str=='+')          //如果第一个字符是正负号
		str++;                        //则移动下一个字符
	while(*str!=0){
		if((*str<'0')||(*str>'9'))    //如果当前字符不是数字
			break;                    //则退出循环
		temp=temp*10+(*str-'0');      //如果当前字符是数字 则计算数值
		str++;                        //移动到下一个字符
	}
	if(*ptr=='-')                     //如果字符串是以“-”开头 则转换成其相反数
		temp=-temp;     
	return temp;
}

int main(){
	char *str="-1234";
	int r=str2int(str);
	cout<<r<<endl;
	
	system("pause");
}
```

##### sprintf函数

位于头文件：stdio.h

函数的原型：int sprintf(char \*buffer, const char \*format, [argument]...);

参数列表

buffer：char型指针，指向将要写入的字符串的缓冲区;

format：格式化字符串;

[argument]...：可选参数，可以是任何类型的数据；

返回值：返回写入buffer的字符数，出错则返回-1。

**1.格式化数字字符串**

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	//把整数123打印成一个字符串保存在s中
	char str1[10];
	int num1=123;
	sprintf(str1,"%d",num1);
	printf("%s\n",str1);

	//可以指定宽度 不足的左边补空格
	char str2[20];
	int num2=123,num3=4567;
	sprintf(str2,"%8d%8d",num2,num3);
	printf("%s\n",str2);

	//可以左对齐
	char str3[20];
	sprintf(str3,"%-8d%-8d",num2,num3);
	printf("%s\n",str3);

	char str4[10],str5[10],str6[10];
	//小写16进制，宽度占8个位置，右对齐
	sprintf(str4,"%8x",num3); 
	//大写16进制，宽度占8个位置，左对齐
	sprintf(str5,"%-8X",num3);
	sprintf(str6,"%08X",num3);
	printf("%s\n",str4);
	printf("%s\n",str5);
	printf("%s\n",str6);

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/66.png)


**2.控制浮点数打印格式**

控制打印的宽度和小数位数**%m.n**格式，其中m表示打印的宽度，n表示小数点后的位数。

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	char str1[20];
	float f1=3.1415926;
	sprintf(str1,"%f",f1);
	printf("%s\n",str1);

	char str2[20],str3[20],str4[20];
	sprintf(str2,"%10.4f",f1);    //指定宽度为10 小数3位 右对齐
	sprintf(str3,"%-10.4f",f1);   //指定宽度为10 小数3位 左对齐
	sprintf(str4,"%.3f",f1);      //不指定宽度 小数3位
	printf("%s\n",str2);
	printf("%s\n",str3);
	printf("%s\n",str4);

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/67.png)

**3.连接字符串**

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	char str1[20];
	char *who = "I";
	char *whom = "CSDN";
	sprintf(str1,"%s love %s.",who,whom);   //产生:"I love CSDN."
	printf("%s\n",str1);

	char str2[20];
	char str3[5]="ab";
	char str4[5]="cd";
	sprintf(str2,"%s%s",str3,str4);
	printf("%s\n",str2);

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/68.png)

**4.打印地址信息**

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	char str1[10];
	char str2[10];
	char str3[10];
	int a=2;

	//无符号整数的"%u"打印
	sprintf(str1,"%u",&a);

	//使用16进制显示一个地址
	sprintf(str2,"%08X",&a);

	//地址打印sprintf提供了专门的"%p"
	sprintf(str3,"%p",&a);

	printf("%s\n",str1);
	printf("%s\n",str2);
	printf("%s\n",str3);

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/69.png)

**5.利用sprintf的返回值**

spritnf返回了本次函数调用最终打印到字符缓冲区中的字符数目。也就是说每当一次sprinf调用结束以后，你无须再调用一次strlen便已经知道了结果字符串的长度。

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	char str1[10];
	int i=123;
	int len=sprintf(str1,"%d",i);
	printf("%s  %d\n",str1,len);

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/70.png)








