---
layout: post
title: 函数指针与指针函数
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

函数指针：首先是一个指针，指向函数的指针；

指针函数：首先是一个函数，返回指针类型数据；

##### 22.函数指针与指针函数的区别

解析：

指针函数是指带指针的函数，即本质是一个函数，并且返回的是某一类型的指针。其定义如下：

```cpp
返回类型标识符 *返回名称（形式参数表）{函数体}
```

事实上，每一个函数，即使它不带有返回类型的指针，它也有一个入口地址，而该地址相当于一个指针。比如函数返回一个整型值，实际上相当于返回一个指针变量值。

函数指针是指向函数的指针变量，因而它首先是指针变量，只不过该指针变量指向函数。有了指向函数的指针变量后，便可以调用函数，如同用指针变量可以引用其他变量一样。

```cpp
#include <iostream>

using namespace std;

int max(int x,int y){
	return (x>y?x:y);
}

float* find(float* p,int x){
	return (p+x);
}


int main(){
	float score[]={10,20,30,40};
	int (*p)(int,int);
	float* q=find(score+1,1);   //30
	int a;
	p=max;
	a=(*p)(1,2);                //2
	cout<<"a="<<a<<endl;        //2
	cout<<"*q="<<*q<<endl;      //30

	system("pause");
}
```

##### 23.数组指针与函数指针的定义

(1).含有十个元素的指针数组

```cpp
int *a[10];
```

(2).数组指针

```cpp
int (*a)[10];
```

(3).函数指针

```cpp
int (*a)(int,int);
```

(4).指向函数的指针数组

```cpp
int (*a[10])(int,int);
```

##### 24.各种指针定义

写出函数指针、函数返回指针、const指针、指向const的指针、指向const的const指针。

函数指针：

```cpp
void (*a)(int,int);
```

函数返回指针：

```cpp
int *a(int,int);
```

const指针（指针不变）：

```cpp
int* const a;
```

指向const的指针：

```cpp
const int* a;
```

指向const的const指针：

```cpp
const int* const a;
```

##### 25.代码改错--函数指针的使用

下面程序要求打印出3个数的最大者，但是程序本身存在问题，请指出：

```cpp
#include <iostream>
using namespace std;

int max(int x,int y){
	return x>y?x:y;
}

int main(){
	int *p;           //函数指针声明出错 int (*p)(int,int);
	int a,b,c;
	int result;
	int max(x,y);     //函数声明出错 int max(int x,int y);
	p=max;
	cout<<"Please input three integer"<<endl;
	cin>>a>>b>>c;
	result=(*p)((*p)(a,b),c);
	cout<<"result="<<result<<endl;
	system("pause");
}
```


##### 26.分析代码写结果--函数指针的使用

```cpp
#include <stdio.h>
#include <stdlib.h>

int add1(int a1,int b1);
int add2(int a2,int b2);

int main(){
	int numa1=1,numb1=2;
	int numa2=2,numb2=3;
	int (*op[2])(int a,int b);
	op[0]=add1;
	op[1]=add2;
	printf("%d %d\n",op[0](numa1,numb1),op[1](numa2,numb2));

	system("pause");
}

int add1(int a1,int b1){
	return (a1+b1);
}

int add2(int a2,int b2){
	return (a2+b2);
}
```


##### 27.typedef用于函数指针定义

下面的定义有什么作用？

```cpp
typedef int(*pfun)(int x,int y);
```

解析：

pfun是一个使用typedef自定义的数据类型。它表示一个函数指针，其参数有两个，都是int类型，返回值也是int类型，都可以按如下步骤使用：

```cpp
typedef int(*pfun)(int x,int y);
int fun(int x,int y);
pfun p=fun;
int ret=p(2,3);
```

定义了一个函数指针类型，表示带2个int参数指向返回值为int的函数指针类型，可以用这种类型来定义函数指针，从而调用相同类型的函数。


