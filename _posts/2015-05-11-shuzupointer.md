---
layout: post
title: 指针数组和数组指针
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

**指针数组：首先是一个数组，即数组的元素时指针形成的**。

**数组指针：首先是一个指针，即指向数组的指针**。

##### 指针数组与数组指针的区别是什么？

解析：

指针数组指一个数组里存放的都是同一个类型的指针，例如：

```cpp
int *a[10];
```

数组a存放了10个int*类型变量，由于它是一个数组，已经在栈区分配了10个(int\*)的空间，在32位机上是40字节，每个空间都可以存放一个int型变量的地址，这时候可以初始化这个数组的每一个元素。

数组指针指向一维或者多维数组的指针，例如：

```cpp
int *b=new int[10];
```

指针b指向含有10个整型数据的一维数组，这个时候释放空间一定要delete[]，否则会造成内存泄漏。

```cpp
#include <iostream>

using namespace std;

int main(){
	int x1[4]={1,2,3,4};
	int x2[2]={5,6};
	int x3[3]={7,8,9};
	int *a[2];              //指针数组
	int *b=x1;              //指针b指向x1数组第一个元素的地址
	int i=0;

	a[0]=x2;                //指针数组两个元素分别指向数组x2和x3
	a[1]=x3;
	cout<<"输出a[0]：";
	for(i=0;i<sizeof(x2)/sizeof(int);i++){
		cout<<a[0][i]<<" ";
	}
	cout<<endl;

	cout<<"输出a[1]：";
	for(i=0;i<sizeof(x3)/sizeof(int);i++){
		cout<<a[1][i]<<" ";
	}
	cout<<endl;

	cout<<"输出b：";
	for(i=0;i<sizeof(x1)/sizeof(int);i++){
		cout<<b[i]<<" ";
	}
	cout<<endl;
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/60.png)


##### 找错--指针数组和数组指针的使用

```cpp
#include <stdio.h>

int main(){
	char *str[]={"Welcome","to","Fortemedia","Nanjing"};
	char **p=str+1;  //p存的是数组1的地址 *p就是str[1]，也就是“to”的地址
	str[0]=(*p++)+2; //出错 等价于*p+2 没有这个内存单元 然后*p指向"Fortemedia"
	str[1]=*(p+1);   //"Nanjing"
	str[2]=p[1]+3;   //"jing"
	str[3]=p[0]+(str[2]-str[1]);
	printf("%s\n",str[0]);
	printf("%s\n",str[1]);
	printf("%s\n",str[2]);
	printf("%s\n",str[3]);
	system("pause");
}
```

程序运行效果：

![](/images/posts/Cpoint/61.png)

