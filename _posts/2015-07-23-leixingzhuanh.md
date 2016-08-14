---
layout: post
title: c++中四种类型转换机制
categories: C和C++基础
description: c++中四种类型转换机制
keywords: c++中四种类型转换机制
---

引用自：http://www.cnblogs.com/bastard/archive/2011/12/14/2288117.html

#### C语言中存在着两种类型转换

隐式转换和显式转换

隐式转换：不同数据类型之间赋值和运算，函数调用传递参数...编译器完成

```cpp
char ch;
int i = ch;
```

显示转换：在类型前增加 ：**（Type）变量** 对变量进行的转换。

```cpp
char *pc = (char*)pb;
void *ps = (void*)pa;
```

通过这两种方式，C语言中大部分的类型转换都可以顺利进行。至于能不能进行转换，转换后的结果如何，编译器不管需要用户自己去控制。

C++继承了C中的隐式和显式转换的方式。但这种转换并不是安全和严格的，加上C++本身对象模型的复杂性，C++增加了四个显示转换的关键字。（C++是强类型语言）:（static_cast，dynamic_cast，const_static，reinterpret_cast）

#### static_cast

##### 用于基本的数据类型转换（char，int等等），及指针之间的转换

```cpp
#include <iostream>
using namespace std;

int main(){
	char a1='a';                           //char转换为int
	int b1=static_cast<int>(a1);
	cout<<b1<<endl;

	int b2=97;
	char a2=static_cast<char>(b2);         //int转换为char
	cout<<a2<<endl;

	double d3=3.145;                       //double转int
	int a3=static_cast<int>(d3);
	cout<<a3<<endl;
	
	int a4=4;                              //int转double
	double d4=static_cast<double>(a4);
	cout<<d4<<endl;

	char* pa = NULL;                       //void转换为其他类型指针
	void *p = static_cast<void*>(pa);
	int *pb = static_cast<int*>(p);
	char *pc = static_cast<char*>(p);
	system("pause");
}
```

##### 类层次结构中基类与子类指针或引用之间的转换

上行转换：子类指针或引用转换成基类表示——安全

下行转换：基类指针或引用转换成子类表示——危险（没有动态类型检查）

```cpp
class A
{
};
class B:public A
{
};
class C:public A
{
};
class D
{
};

A objA;
B objB;
A* pObjA = new A();
B* pObjB = new B();
C* pObjC = new C();
D* pObjD = new D();

objA = static_cast<A&>(objB);     //转换为基类引用    
objA = static_cast<A>(objB);
objA=objB;
objB = static_cast<B>(objA);      //error 不能进行转换  

pObjA = pObjB;                    //right 基类指针指向子类对象
pobjB = pobjA;                    //error 子类指针指向基类对象
pObjA = static_cast<A*>(pObjB);   //right 基类指针指向子类
pObjB = static_cast<B*>(pObjA);   //强制转换 OK基类到子类
pObjC = static_cast<C*>(pObjB);   //error 继承于统一类的派生指针之间转换 
pObjD = static_cast<D*>(pObjC);   //error 两个无关联之间转换
```




![](/images/posts/C++/217.png)