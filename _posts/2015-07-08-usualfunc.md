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


#### char*字符串函数

##### strcpy

即strcpy只用于字符串复制，并且它不仅复制字符串内容之外，还会复制字符串的结束符。

```cpp
char* strcpy(char* des,const char* source){
	char* r=des;
	assert((des != NULL) && (source != NULL));
　	while((*des++ = *source++)!='\0');
　	return r;
}
```

##### memcpy

```cpp
void *memcpy2(void *memTo,const void *memFrom,size_t size){
	assert((memTo!=NULL)&&(memFrom!=NULL));     //保存memTo和memFrom必须有效
	char *tempFrom=(char*)memFrom;              //保存memFrom的首地址
	char *tempTo=(char*)memTo;                  //保存memTo首地址
	while(size-->0)                             //循环size次 复制memFrom的值到memTo中
		*tempTo++=*tempFrom++;
	return memTo;
}
```

size是字节的大小，表示要复制多少个字节。memcpy可以复制任意内容，例如字符数组、整型、结构体、类等等。

##### strlen

求char*的长度，以“\0”结束判读。

##### strcmp

实现C/C++库函数中的strcmp函数。对于两个字符串str1和str2，若相等则返回0，若str1大于str2则返回1，若str1小于str2返回-1。

```cpp
int strcmp(const char *src,const char *dst){
	int ret=0;
	//循环比较两个字符是否相等
	//如果不等或者到了dst字符串末尾 退出循环
	while(!(ret=*(unsigned char *)src-*(unsigned char *)dst)&&*dst){
		++src;
		++dst;
	}
	if(ret<0)               //ret保存着字符比较的结果
		ret=-1;
	else if(ret>0)
		ret=1;
	return ret;
}
```

##### strrev

字符串反转函数：strrev，例如输入“abcd”，输出为“dcba”。

```cpp
char *strrev(const char *str){         //通过数组下标方式访问字符串
	int len=strlen(str);
	char *tmp=new char[len+1];
	strcpy(tmp,str);
	for(int i=0;i<len/2;i++){
		char c=tmp[i];
		tmp[i]=tmp[len-i-1];
		tmp[len-i-1]=c;
	}
	return tmp;
}
```

##### strcat

使用strcat连接字符串。

```cpp
#include <iostream>
using namespace std;
int main(){
	char *str1="abc";
	char *str2="ABC";
	char str[10];
	sprintf(str,"%s%s",str1,str2);
	cout<<str<<endl;
	char str3[10];
	str3[0]='\0';       //str3[0]赋为结束符'\0' 以便strcat能正常使用 
	strcat(str3,str1);  //str3变为"abc"
	strcat(str3,str2);  //str3变为"abcABC"
	cout<<str3<<endl;
	system("pause");
}
```

```cpp
void strcat(char *str1,const char *str2){
	int pos=0;
	while(str1[pos]!='\0')       //定位字符串str1的末尾
		pos++;

	int len=0;                   //计算字符串str2的长度
	while(str2[len]!='\0')
		len++;

	for(int i=0;i<=len;i++){
		str1[pos+i]=str2[i];
	}
	cout<<str1<<endl;
}
```

#### 关于string的函数

需包含头文件：include <string>

##### string初始化

```cpp
string str="abc";       //直接初始化

str1.push_back('a');    //压入单个字符

string str1="ABC";       
string str2=str1;       //互相赋值

char c[10]="abc";       //char*初始化
string str3(c);

string str4(5,'a');     //用n个字符c初始化
```

##### 返回string大小和长度

```cpp
string str="affds"; 
cout<<str.size()<<" "<<str.length()<<endl;  //打印为 5  5
```

##### 返回string的当前容量(即string中不必增加内存即可存放的元素个数)

```cpp
string str="affds"; 
cout<<str.capacity()<<endl;    //打印  15
```

##### 判断string是否为空

```cpp
string str="affds"; 
cout<<str.empty()<<endl;       //打印 0
```

##### 两个string的连接

```cpp
string str1="ABC"; 
string str2="abc";
string str=str1+str2;
cout<<str<<endl;              //打印ABCabc
```

##### string的比较

**运算符">","<",">=","<=","!="均被重载用于字符串的比较**

```cpp
string str1="ABC"; 
string str2="abc";
cout<<(str1>str2)<<endl;           //0
cout<<(str1<str2)<<endl;           //1
cout<<(str1==str2)<<endl;          //0
cout<<str1.compare(str2)<<endl;    //-1   大于输出1  小于输出-1  等于输出0
```

##### string的子串

```cpp
string str="abcdefg"; 
string strsub=str.substr(0,3);
cout<<strsub<<endl;                //打印abc
```

##### string查找函数

**int find(char c, int pos = 0) const：**从pos开始查找字符c在当前字符串的位置。

```cpp
string str="abcdefg"; 
cout<<str.find('c',0)<<endl;       //打印 2
```

##### string替换函数

**string &replace(int p0, int n0,const string &s)：**删除从p0开始的n0个字符，然后在p0处插入串s

**string &replace(int p0, int n0,const char \*s)：**删除从p0开始的n0个字符，然后在p0处插入串s

**string &replace(int p0, int n0,int n, char c)：**删除p0开始的n0个字符，然后在p0处插入n个字符c

```cpp
string str="abcdefg"; 
str.replace(0,2,"ABC");
cout<<str<<endl;          //打印ABCdefg
```

##### string插入函数

在p0位置插入串s：

```cpp
string &insert(int p0, const char *s);
string &insert(int p0,const string &s);
```

在p0位置插入字符串s中pos开始的前n个字符：

```cpp
string &insert(int p0, const char *s, int n);
string &insert(int p0,const string &s, int pos, int n);
```

在p0处插入n个字符c：

```cpp
string &insert(int p0, int n, char c);
```

```cpp
string str="abcdefg"; 
str.insert(2,"ABC");
cout<<str<<endl;            //输出abABCcdefg
```

##### string的删除函数

**string &erase(int pos = 0, int n = npos)：**删除pos开始的n个字符，返回修改后的字符串

```cpp
string str="abcdefg"; 
str.erase(1,3);
cout<<str<<endl;                     //aefg
```

##### string反序

```cpp
string str="abcdefg"; 
string s(str.rbegin(),str.rend());
cout<<s<<endl;                       //gfedcba
```

##### string排序

```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;
int main(){
	string str="dcsazg"; 
	sort(str.begin(),str.end());
	cout<<str<<endl;               //acdgsz
	system("pause");
}
```

##### string中的front函数和back函数

```cpp
string str="dcsazg"; 
cout<<str.front()<<" "<<str.back()<<endl;  //d  g
```

#### 申请内存函数

##### malloc

```cpp
int *m=(int*)malloc(5*sizeof(int));
```

##### free

```cpp
free(m);
```

##### realloc

```cpp
int *m=(int*)malloc(5*sizeof(int));
m=(int*)realloc(m,(10+10)*sizeof(int));
```