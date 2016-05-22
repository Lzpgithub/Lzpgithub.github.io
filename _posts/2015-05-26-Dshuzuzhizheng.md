---
layout: post
title: 对象数组和对象指针
categories: C和C++基础
description: 对象数组和对象指针
keywords: 类，对象数组，对象指针
---

#### 对象数组

数组不仅可以由简单变量组成(例如整型数组的每一个元素都是整型变量)，也可以由对象组成(对象数组的每一个元素都是同类的对象)。

一个班有50个学生，每个学生的属性包括姓名、性别、年龄、成绩等。如果为每一个学生建立一个对象，需要分别取50个对象名。用程序处理很不方便。这时可以定义一个“学生类”对象数组，每一个数组元素是一个“学生类”对象。例如：

```cpp
Student stud[50];    //假设已声明了Student类，定义stud数组，有50个元素
```

在建立数组时，同样要调用构造函数。如果有50个元素，需要调用50次构造函数。在需要时可以在定义数组时提供实参以实现初始化。如果构造函数只有一个参数，在定义数组时可以直接在等号后面的花括号内提供实参。如：

```cpp
Student stud[3]={60,70,78};    //合法，3个实参分别传递给3个数组元素的构造函数
```

如果构造函数有多个参数，则不能用在定义数组时直接提供所有实参的方法，因为一个数组有多个元素，对每个元素要提供多个实参，如果再考虑到构造函数有默认参数的情况，很容易造成实参与形参的对应关系不清晰，出现歧义性。例如，类Student的构造函数有多个参数，且为默认参数：

```cpp
Student::Student(int=1001,int=18,int=60); //定义构造函数，有多个参数，且为默认参数
```

如果采用上面这种方法会容易引起歧义性。

如果构造函数有多个参数，则可以这样定义对象数组：

```cpp
Student Stud[3]={        //定义对象数组
	Student(1001,18,87), //调用第1个元素的构造函数，为它提供3个实参
	Student(1002,19,76), //调用第2个元素的构造函数，为它提供3个实参
	Student(1003,18,72)  //调用第3个元素的构造函数，为它提供3个实参
};
```

在建立对象数组时，分别调用构造函数，对每个元素初始化。每一个元素的实参分别用括号包起来，对应构造函数的一组形参。

对象数组的使用方法：

```cpp
#include <iostream>
using namespace std;
class Box
{public:
	//声明有默认参数的构造函数，用参数初始化表对数据成员初始化
	Box(int h=10,int w=12,int len=15): height(h),width(w),length(len){ }

	int volume( );
 private:
	int height;
	int width;
 	int length;
};

int Box::volume( )
{
	return(height*width*length);
}

intmain( )
{ 
	Box a[3]={      //定义对象数组
		Box(10,12,15),  //调用构造函数Box，提供第1个元素的实参
		Box(15,18,20),  //调用构造函数Box，提供第2个元素的实参
		Box(16,20,26)   //调用构造函数Box，提供第3个元素的实参
	};

	cout<<"volume of a[0] is"<<a[0].volume( )<<endl;  //调用a[0]的volume函数
	cout<<"volume of a[1] is"<<a[1].volume( )<<endl;  //调用a[1]的volume函数
	cout<<"volume of a[2] is"<<a[2].volume( )<<endl;  //调用a[2]的volume函数
}
```

运行结果：

![](/images/posts/C++/20.png)


#### 对象指针

##### 指向对象的指针

在建立对象时，编译系统会为每一个对象分配一定的存储空间，以存放其成员。对象空间的起始地址就是对象的指针。可以定义一个指针变量，用来存放对象的指针。如果有一个类：

```cpp
class Time
{public:
	int hour;
	int minute;
	int sec;
	void get_time( );
};

void Time::get_time( )
{
	cout<<hour<<":"<<minute<<":"<<sec<<endl;
}

Time *pt;   //定义pt为指向Time类对象的指针变量
Time t1;    //定义t1为Time类对象
pt=&t1;     //将t1的起始地址赋给pt
```

pt就是指向Time类对象的指针变量，它指向对象t1。

定义指向类对象的指针变量的一般形式为：

```cpp
类名 *对象指针名；
```

可以通过对象指针访问对象和对象的成员。如：

```cpp
*pt pt;            //所指向的对象，即t1。
(*pt).hour;        //pt所指向的对象中的hour成员，即t1.hour
pt->hour;          //pt所指向的对象中的hour成员，即t1.hour
(*pt).get_time();  //调用pt所指向的对象中的get_time函数，即t1.get_time
pt->get_time();    //调用pt所指向的对象中的get_time函数，即t1.get_time
```

##### 指向对象成员的指针

对象有地址，存放对象初始地址的指针变量就是指向对象的指针变量。对象中的成员也有地址，存放对象成员地址的指针变量就是指向对象成员的指针变量。

1.指向对象数据成员的指针

定义指向对象数据成员的指针变量的方法和定义指向普通变量的指针变量方法相同。

定义指向对象数据成员的指针变量的一般形式为：

```cpp
数据类型名 *指针变量名；
```

如果Time类的数据成员hour为公用的整型数据，则可以在类外通过指向对象数据成员的指针变量访问对象数据成员hour。

```cpp
int *p1;          //定义指向整型数据的指针变量
p1=&t1.hour;      //将对象t1的数据成员hour的地址赋给p1，p1指向t1.hour
cout<<*p1<<endl;  //输出t1.hour的值
```

2.指向对象成员函数的指针

定义指向对象成员函数的指针变量的方法和定义指向普通函数的指针变量方法有所不同。

成员函数与普通函数有一个最根本的区别：它是类中的一个成员。编译系统要求在上面的赋值语句中，指针变量的类型必须与赋值号右侧函数的类型相匹配，要求在以下3方面都要匹配：

(1).函数参数的类型和参数个数；

(2).函数返回值的类型；

(3).所属的类。

定义指向成员函数的指针变量应该采用下面的形式：

```cpp
void (Time::*p2)(); //定义p2为指向Time类中公用成员函数的指针变量
```

定义指向公用成员函数的指针变量的一般形式为：

```cpp
数据类型名 (类名::*指针变量名)(参数表列);
```

可以让它指向一个公用成员函数，只需把公用成员函数的入口地址赋给一个指向公用成员函数的指针变量即可。如：

```cpp
p2=&Time::get_time;
```

使指针变量指向一个公用成员函数的一般形式为：

```cpp
指针变量名=&类名::成员函数名;
```

有关对象指针的使用方法：

```cpp
#include <iostream>
using namespace std;
class Time
{public:
	Time(int,int,int);
	int hour;
	int minute;
	int sec;
	void get_time( );    //声明公有成员函数
};

Time::Time(int h,int m,int s)
{
	hour=h;
	minute=m;
	sec=s;
}

void Time::get_time( ) //定义公有成员函数
{
	cout<<hour<<":"<<minute<<":"<<sec<<endl;
}

int main()
{
	Time t1(10,13,56);   //定义Time类对象t1
	int*p1=&t1.hour;     //定义指向整型数据的指针变量p1，并使p1指向t1.hour
	cout<<*p1<<endl;     //输出p1所指的数据成员t1.hour
	t1.get_time();       //调用对象t1的成员函数get_time
	Time*p2=&t1;         //定义指向Time类对象的指针变量p2，并使p2指向t1
	p2->get_time();      //调用p2所指向对象(即t1)的get_time函数
	void (Time::*p3)();  //定义指向Time类公用成员函数的指针变量p3
	p3=&Time::get_time;  //使p3指向Time类公用成员函数get_time
	(t1.*p3)();          //调用对象t1中p3所指的成员函数(即t1.get_time( ))
}
```

程序输出结果为：

![](/images/posts/C++/21.png)


##### this指针

每个对象中的数据成员都分别占有存储空间，如果对同一个类定义了n个对象，则有n组同样大小的空间以存放n个对象中的数据成员。但是，不同对象都调用同一个函数代码段。那么，当不同对象的成员函数引用数据成员时，怎么能保证引用的是所指定的对象的数据成员呢？

定义的Box类，定义了3个同类对象a,b,c。如果有a.volume()，应该是引用对象a中的height，width
和length，计算出长方体a的体积。如果有b.volume( )，应该是引用对象b中的height，width和length计算出长方体b的体积。而现在都用同一个函数段，系统怎样使它分别引用a或b中的数据成员呢？

**在每一个成员函数中都包含一个特殊的指针，这个指针的名字是固定的，称为this。它是指向本类对象的指针，它的值是当前被调用的成员函数所在的对象的起始地址**。例如，当调用成员函数a.volume时，编译系统就把对象a的起始地址赋给this指针，于是在成员函数引用数据成员时，就按照this的指向找到对象a的数据成员。例如volume函数要计算height\*width\*length的值实际上是执行：

```cpp
(this->height)*(this->width)*(this->length)
```

由于当前this指向a，因此相当于执行：

```cpp
(a.height)*(a.width)*(a.length)
```

同样如果有b.volume()，编译系统就把对象b的起始地址赋给成员函数volume的this指针，显然计算出来的是长方体b的体积。

this指针式隐式使用的，它是作为参数被传递给成员函数，本来成员函数volume的定义如下：

```cpp
int Box::volume( )
{
	return (height*width*length);
}
```

C++把它处理为：

```cpp
int Box::volume(Box *this)
{
	return(this->height*this->width*this->length);
}
```

即在成员函数的形参表列中增加一个this指针。在调用该成员函数时，实际上是用以下方式调用的：

```cpp
a.volume(&a);
```

将对象a的地址传给形参this指针。然后按this的指向去引用其他成员。

这些都是编译系统自动实现的，编程序者不必人为地在形参中增加this指针，也不必将对象a的地址传给this指针。在需要时也可以显式地使用this指针。例如在Box类中的volume函数中，下面两种表示方法都是合法的、相互等价的：

```cpp
return (height*width*length);                     //隐含使用this指针
return (this->height*this->width*this->length);   //显式使用this指针
```

可以用\*this表示被调用的成员函数所在的对象，\*this就是this所指向的对象，即当前的对象。例如在成员函数a.volume( )的函数体中，如果出现\*this，它就是本对象a。上面的return语句也可写成：

```cpp
return((*this).height*(*this).width*(*this).length);
```

注意\*this两侧的括号不能省略，不能写成：

```cpp
*this.height
```

所谓“调用对象a的成员函数f”，实际上是在调用成员函数f时使this指针指向对象a，从而访问对象a的成员。在使用“调用对象a的成员函数f”时，应当对它的含义有正确的理解。