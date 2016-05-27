---
layout: post
title: 友元函数和友元类
categories: C和C++基础
description: 友元函数和友元类
keywords: 友元函数，友元类
---

如果在本类以外的其他地方定义了一个函数(这个函数可以是不属于任何类的非成员函数，也可以是其他类的成员函数)，在类体中用friend对其进行声明，此函数就称为本类的友元函数。**友元函数可以访问这个类中的私有成员**。

#### 友元函数

##### 1.将普通函数声明为友元函数

```cpp
#include <iostream>
using namespace std;
class Time
{
public:
	Time(int,int,int);
	friend void display(Time &);   //声明display函数为Time类的友元函数
private:                           //以下数据是私有数据成员
	int hour;
	int minute;
	int sec;
};

Time::Time(int h,int m,int s){    //构造函数，给hour,minute,sec赋初值
	hour=h;
	minute=m;
	sec=s;
}

void display(Time& t){             //这是友元函数，形参t是Time类对象的引用，但是没有this指针
	cout<<t.hour<<":"<<t.minute<<":"<<t.sec<<endl;
}

int main( )
{ 
	Time t1(10,13,56);
	display(t1);                  //调用display函数，实参t1是Time类对象
	system("pause");            
}
```

由于声明了display是Time类的friend函数，所以display函数可以引用Time中的私有成员hour，minute，sec。但注意在引用这些私有数据成员时，必须加上对象名，不能写成：

```cpp
cout<<hour<<":"<<minute<<":"<<sec<<endl;
```

因为display函数不是Time类的成员函数，不能默认引用Time类的数据成员，必须指定要访问的对象。


##### 2.友元成员函数

friend函数不仅可以是一般函数(非成员函数)，而且可以是另一个类中的成员函数。

友元成员函数的简单应用：

```cpp
#include <iostream>
using namespace std;

class Date;                 //对Date类的提前引用声明

class Time                  //定义Time类
{
public:
	Time(int,int,int);
	void display(Date &);   //display是成员函数，形参是Date类对象的引用
private:
	int hour;
	int minute;
	int sec;
};

class Date                              //声明Date类
{
public:
	Date(int,int,int);
	friend void Time::display(Date &);  //声明Time中的display函数为友元成员函数
private:
	int month;
	int day;
	int year;
};

Time::Time(int h,int m,int s)          //类Time的构造函数
{
	hour=h;
	minute=m;
	sec=s;
}

void Time::display(Date &d){           //display的作用是输出年、月、日和时、分、秒
	cout<<d.month<<"/"<<d.day<<"/"<<d.year<<endl; //引用Date类对象中的私有数据
	cout<<hour<<":"<<minute<<":"<<sec<<endl;      //引用本类对象中的私有数据
}

Date::Date(int m,int d,int y){         //类Date的构造函数
	month=m;
	day=d;
	year=y;
}

int main()
{
	Time t1(10,13,56);      //定义Time类对象t1
	Date d1(12,25,2004);    //定义Date类对象d1
	t1.display(d1);         //调用t1中的display函数，实参是Date类对象d1
	system("pause");
}
```

##### 3.一个函数(包括普通函数和成员函数)可以被多个类声明为“朋友”

如此就可以引用多个类中的私有数据。


#### 友元类

不仅可以将一个函数声明为一个类的“朋友”，而且可以将一个类(例如B类)声明为另一个类(例如A类)的“朋友”。这时B类就是A类的友元类。友元类B中的所有函数都是A类的友元函数，可以访问A类中的所有成员。在A类的定义体中用以下语句声明B类为其友元类：

```cpp
friend B;
```

声明友元类的一般形式为：

```cpp
friend 类名;
```

关于友元，有两点需要说明：

(1)友元的关系是单向的而不是双向的。

(2)友元的关系不能传递。

在实际工作中，除非确有必要，一般并不把整个类声明为友元类，而只将确实有需要的成员函数声明为友元函数，这样更安全一些。关于友元利弊的分析：

面向对象程序设计的一个基本原则是封装性和信息隐蔽，而友元却可以访问其他类中的私有成员，不能不说这是对封装原则的一个小的破坏。但是它能有助于数据共享，能提高程序的效率，在使用友元时，要注意到它的副作用，不要过多地使用友元，只有在使用它能使程序精炼，并能大大提高程序的效率时才用友元。