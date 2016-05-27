---
layout: post
title: 运算符重载
categories: C和C++基础
description: 运算符重载
keywords: 运算符重载
---

#### 什么是运算符重载

所谓重载，就是重新赋予新的含义。函数重载就是对一个已有的函数赋予新的含义，使之实现新功能。运算符也可以重载。实际上，我们已经在不知不觉之中使用了运算符重载。

用户能否根据自己的需要对C++已提供的运算符进行重载，赋予它们新的含义，使之一名多用。譬如，能否用“+”号进行两个复数的相加。

用户必须自己设法实现复数相加。例如用户可以通过定义一个专门的函数来实现复数相加：

```cpp
#include <iostream>
using namespace std;
class Complex //定义Complex类
{
public:
	Complex( ){real=0;imag=0;}                 //定义构造函数
	Complex(double r,double i){real=r;imag=i;} //构造函数重载
	Complex complex_add(Complex &c2);          //声明复数相加函数
	void display( );                           //声明输出函数
private:
	double real;     //实部
	double imag;     //虚部
};

Complex Complex::complex_add(Complex &c2)
{
	Complex c;
	c.real=real+c2.real;
	c.imag=imag+c2.imag;
	return c;
}

void Complex::display( )    //定义输出函数
{
	cout<<"("<<real<<","<<imag<<"i)"<<endl;
}

int main( )
{
	Complex c1(3,4),c2(5,-10),c3; //定义3个复数对象
	c3=c1.complex_add(c2);        //调用复数相加函数
	cout<<"c1="; 
	c1.display( );                //输出c1的值
	cout<<"c2="; 
	c2.display( );                //输出c2的值
	cout<<"c1+c2="; 
	c3.display( );                //输出c3的值
	system("pause");
}
```

运行结果：

![](/images/posts/Cpoint/30.png)

结果无疑是正确的，但调用方式不直观、太烦琐，使人感到很不方便。能否也和整数的加法运算一样，直接用加号“+”来实现复数运算呢？如c3=c1+c2，编译系统就会自动完成c1和c2两个复数相加的运算。如果能做到，就为对象的运算提供了很大的方便。这就需要对运算符“+”进行重载。

#### 运算符重载的方法

运算符重载的方法是定义一个重载运算符的函数，在需要执行被重载的运算符时，系统就自动调用该函数，以实现相应的运算。也就是说，运算符重载是通过定义函数实现的。运算符重载实质上是函数的重载。

重载运算符的函数一般格式如下：

函数类型 operator 运算符名称 (形参表列){对运算符的重载处理}

例如，想将“+”用于Complex类(复数)的加法运算，函数的原型可以是这样的：

```cpp
Complex operator+ (Complex& c1,Complex& c2);
```

在定义了重载运算符的函数后，可以说：函数operator+重载了运算符+。为了说明在运算符重载后，执行表达式就是调用函数的过程，可以把两个整数相加也想像为调用下面的函数：

```cpp
int operator + (int a,int b)
{
	return (a+b);
}
````

```cpp

```