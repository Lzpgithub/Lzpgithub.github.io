---
layout: post
title: 继承与派生
categories: C和C++基础
description: 继承与派生
keywords: 继承，派生
---

面向对象程序设计有4个主要特点:**抽象、封装、继承和多态性**。面向对象技术强调软件的可重用性(software reusability)。C++语言提供了类的继承机制，解决了软件重用问题。

#### 继承与派生的概念

在C++中可重用性是通过继承(inheritance)这一机制来实现的。继承是C++的一个重要组成部分。一个类中包含了若干数据成员和成员函数。在不同的类中，数据成员和成员函数是不相同的。但有时两个类的内容基本相同或有一部分相同。**利用原来声明的类作为基础，再加上新的内容即可，以减少重复的工作量**。C++提供的继承机制就是为了解决这个问题。

**在C++中，所谓“继承”就是在一个已存在的类的基础上建立一个新的类**。已存在的类(例如“马”)称为“基类(base class)”或“父类(father class)”。新建立的类(例如“公马”)称为“派生类(derived class)”或“子类(son class)”。如图所示：

![](/images/posts/C++/101.png)

一个新类从已有的类那里获得其已有特性，这种现象称为类的继承。通过继承，一个新建子类从已有的父类那里获得父类的特性。从另一角度说，从已有的类(父类)产生一个新的子类，称为类的派生。类的继承是用已有的类来建立专用类的编程技术。派生类继承了基类的所有数据成员和成员函数，并可以对成员作必要的增加或调整。一个基类可以派生出多个派生类，每一个派生类又可以作为基类再派生出新的派生类，因此基类和派生类是相对而言的。

最简单的情况:一个派生类只从一个基类派生，这称为单继承(single inheritance)，这种继承关系所形成的层次是一个树形结构：

![](/images/posts/C++/102.png)

图中箭头的方向，箭头表示继承的方向，从派生类指向基类。

一个派生类不仅可以从一个基类派生，也可以从多个基类派生。一个派生类有两个或多个基类的称为多重继承(multiple inheritance)，这种继承关系所形成的结构：

![](/images/posts/C++/103.png)

关于基类和派生类的关系，可以表述为:派生类是基类的具体化，而基类则是派生类的抽象：

![](/images/posts/C++/104.png)

#### 派生类的声明方式

假设已经声明了一个基类Student，在此基础上通过单继承建立一个派生类Student1:

```cpp
class Student1: public Student    //声明基类是Student
{public:
	void display_1( )             //新增加的成员函数
	{
		cout<<"age:"<<age<<endl;
		cout<<"address:"<<addr<<endl;
	}
private:
	int age;                      //新增加的数据成员
    string addr;                  //新增加的数据成员
};
```

基类名前面有public的称为“公用继承(publicinheritance)”。

声明派生类的一般形式为：

```cpp
class 派生类名:［继承方式］基类名
{
	派生类新增加的成员
};
```

**继承方式包括:public(公用的),private(私有的)和protected(受保护的)，此项是可选的，如果不写此项，则默认为private(私有的)**。

#### 派生类的构成

派生类中的成员包括从基类继承过来的成员和自己增加的成员两大部分。在基类中包括数据成员和成员函数(或称数据与方法)两部分，派生类分为两大部分:

一部分是从基类继承来的成员；

另一部分是在声明派生类时增加的部分。每一部分均分别包括数据成员和成员函数。

![](/images/posts/C++/105.png)

实际上，并不是把基类的成员和派生类自己增加的成员简单地加在一起就成为派生类。构造一个派生
类包括以下3部分工作:

1.**从基类接收成员**。派生类把基类全部的成员(不包括构造函数和析构函数)接收过来，也就是说是没有选择的，不能选择接收其中一部分成员，而舍弃另一部分成员。

2.**调整从基类接收的成员**。接收基类成员是程序人员不能选择的，但是程序人员可以对这些成员作某些调整。

3.**在声明派生类时增加的成员**。这部分内容是很重要的，它体现了派生类对基类功能的扩展。

此外，在声明派生类时，一般还应当自己定义派生类的构造函数和析构函数，因为构造函数和析构函数是不能从基类继承的。

**派生类是基类定义的延续。可以先声明一个基类，在此基类中只提供某些最基本的功能，而另外有些功能并未实现，然后在声明派生类时加入某些具体的功能，形成适用于某一特定应用的派生类。通过对基类声明的延续，将一个抽象的基类转化成具体的派生类。因此，派生类是抽象基类的具体实现**。

```cpp
#include <iostream>

using namespace std;

class A{
public:
	A(){
		a1=1;
		a2=2;
		a3=3;
	}
	void showA(){
		cout<<a1<<" "<<a2<<" "<<a3<<endl;
	}
private:
	int a1;
	char a2;
	int a3;
};

class B:public A{
public:
	B(){
		b1=4;
		b2=5;
	}
	void show(){
		cout<<b1<<" "<<b2;
	}
private:
	int b1;
	char b2;
};
	

int main(){
	cout<<sizeof(A)<<endl;       //12 要考虑内存对齐问题
	cout<<sizeof(B)<<endl;       //12+8=20;
	system("pause");
}
```

#### 派生类成员的访问属性

既然派生类中包含基类成员和派生类自己增加的成员，就产生了这两部分成员的关系和访问属性的问题。在建立派生类的时候，并不是简单地把基类的私有成员直接作为派生类的私有成员，把基类的公用成员直接作为派生类的公用成员。实际上，对基类成员和派生类自己增加的成员是按不同的原则处理的。

具体说，在讨论访问属性时，要考虑以下几种情况：

(1)基类的成员函数访问基类成员。

(2)派生类的成员函数访问派生类自己增加的成员。

(3)基类的成员函数访问派生类的成员。

(4)派生类的成员函数访问基类的成员。

(5)在派生类外访问派生类的成员。

(6)在派生类外访问基类的成员。

对于第(1)和第(2)种情况，比较简单，常用规则处理，即:基类的成员函数可以访问基类成员，派生类的成员函数可以访问派生类成员。私有数据成员只能被同一类中的成员函数访问，公用成员可以被外界访问。

第(3)种情况也比较明确，基类的成员函数只能访问基类的成员，而不能访问派生类的成员。第(5)种情况也比较明确，在派生类外可以访问派生类的公用成员，而不能访问派生类的私有成员。

对于第(4)和第(6)种情况，就稍微复杂一些，也容易混淆。

**在派生类中，对基类的继承方式可以有public(公用的)，private(私有的)和protected(保护的)3种。不同的继承方式决定了基类成员在派生类中的访问属性**。

**（1）公用继承(public inheritance)**

基类的公用成员和保护成员在派生类中保持原有访问属性，其私有成员仍为基类私有。

**（2）私有继承(private inheritance)**

基类的公用成员和保护成员在派生类中成了私有成员。其私有成员仍为基类私有。

**（3）受保护的继承(protected inheritance)**

基类的公用成员和保护成员在派生类中成了保护成员，其私有成员仍为基类私有。保护成员的意思是：不能被外界引用，但可以被派生类的成员引用。


#### 公有继承

在定义一个派生类时将基类的继承方式指定为public的，称为公用继承，用公用继承方式建立的派生类称为公用派生类(public derived class)，其基类称为公用基类(public base class)。

采用公用继承方式时，基类的公用成员和保护成员在派生类中仍然保持其公用成员和保护成员的属性，而基类的私有成员在派生类中并没有成为派生类的私有成员，它仍然是基类的私有成员，只有基类的成员函数可以引用它，而不能被派生类的成员函数引用，因此就成为派生类中的不可访问的成员。

访问公有基类的成员：

```cpp
#include <iostream>
#include <string>

using namespace std;

class Student                            //声明基类
{
public:                                  //基类公用成员
	void get_value( ){
		cin>>num>>name>>sex;
	}

	void display( ){
		cout<<"num:"<<num<<endl;
		cout<<"name:"<<name<<endl;
		cout<<"sex:"<<sex<<endl;
	}
private :                                //基类私有成员
	int num;
	string name;
	char sex;
};

class Student1: public Student           //以public方式声明派生类Student1
{public:
	void display_1( ){
		cout<<"num:"<<num<<endl;         //企图引用基类的私有成员，错误
		cout<<"name:"<<name<<endl;       //企图引用基类的私有成员，错误
		cout<<"sex:"<<sex<<endl;         //企图引用基类的私有成员，错误
		cout<<"age:"<<age<<endl;         //引用派生类的私有成员，正确
		cout<<"address:"<<addr<<endl;    //引用派生类的私有成员，正确
	} 
private:
	int age;
	string addr;
};

int main(){
	Student1 stud;           //定义派生类Student1的对象stud
	stud.display( );         //调用基类的公用成员函数，输出基类中3个数据成员的值
	stud.display_1();        //调用派生类的公用成员函数，输出派生类中两个数据成员的值
	return 0;
}
```

#### 私有继承

在声明一个派生类时将基类的继承方式指定为private的，称为私有继承，用私有继承方式建立的派生类称为私有派生类(private derived class)，其基类称为私有基类(private base class)。

私有基类的公用成员和保护成员在派生类中的访问属性相当于派生类中的私有成员，即派生类的成员函数能访问它们，而在派生类外不能访问它们。私有基类的私有成员在派生类中成为不可访问的成员，只有基类的成员函数可以引用它们。一个基类成员在基类中的访问属性和在派生类中的访问属性可能是不同的。

![](/images/posts/C++/106.png)

只需理解：既然声明为私有继承，就表示将原来能被外界引用的成员隐藏起来，不让外界引用，因此私有基类的公用成员和保护成员理所当然地成为派生类中的私有成员。私有基类的私有成员按规定只能被基类的成员函数引用，在基类外当然不能访问他们，因此它们在派生类中是隐蔽的，不可访问的。对于不需要再往下继承的类的功能可以用私有继承方式把它隐蔽起来，这样，下一层的派生类无法访问它的任何成员。可以知道:一个成员在不同的派生层次中的访问属性可能是不同的，它与继承方式有关。

写出私有派生类如下:

```cpp
#include <iostream>
#include <string>

using namespace std;

class Student                            //声明基类
{
public:                                  //基类公用成员
	void get_value( ){
		cin>>num>>name>>sex;
	}

	void display( ){
		cout<<"num:"<<num<<endl;
		cout<<"name:"<<name<<endl;
		cout<<"sex:"<<sex<<endl;
	}
private :                                //基类私有成员
	int num;
	string name;
	char sex;
};

class Student1: private Student          //用私有继承方式声明派生类Student1
{
public:
	void display_1()                     //输出两个数据成员的值
	{
		cout<<"age:"<<age<<endl;         //引用派生类的私有成员，正确
		cout<<"address:"<<addr<<endl;    //引用派生类的私有成员，正确
	}
private:
	int age;
	string addr;
};

//请分析下面的主函数:
int main()
{
	Student1 stud1;      //定义一个Student1类的对象stud1
	stud1.display();     //错误，私有基类的公用成员函数在派生类中是私有函数
	stud1.display_1( );  //正确。Display_1函数是Student1类的公用函数
	stud1.age=18;        //错误。外界不能引用派生类的私有成员
	return 0;
}
```

#### 保护成员和保护继承

由protected声明的成员称为“受保护的成员”，或简称“保护成员”。从类的用户角度来看，保护成员等价于私有成员。但有一点与私有成员不同，保护成员可以被派生类的成员函数引用。

![](/images/posts/C++/107.png)

如果基类声明了私有成员，那么任何派生类都是不能访问它们的，若希望在派生类中能访问它们，应当把它们声明为保护成员。如果在一个类中声明了保护成员，就意味着该类可能要用作基类，在它的派生类中会访问这些成员。

在定义一个派生类时将基类的继承方式指定为protected的，称为保护继承，用保护继承方式建立的派生类称为保护派生类(protected derivedclass)，其基类称为受保护的基类(protected baseclass)，简称保护基类。

保护继承的特点是:保护基类的公用成员和保护成员在派生类中都成了保护成员，其私有成员仍为基类私有。也就是把基类原有的公用成员也保护起来，不让类外任意访问。

保护基类的所有成员在派生类中都被保护起来，类外不能访问，其公用成员和保护成员可以被其派生类的成员函数访问。比较一下私有继承和保护继承(也就是比较在私有派生类中和在保护派生类中的访问属性)，可以发现，在直接派生类中，以上两种继承方式的作用实际上是相同的:在类外不能访问任何成员，而在派生类中可以通过成员函数访问基类中的公用成员和保护成员。

如果以公用继承方式派生出一个新派生类，原来私有基类中的成员在新派生类中都成为不可访问的成员，无论在派生类内或外都不能访问，而原来保护基类中的公用成员和保护成员在新派生类中为保护成员，可以被新派生类的成员函数访问。

如果善于利用保护成员，可以在类的层次结构中找到数据共享与成员隐蔽之间的结合点。既可实现某些成员的隐蔽，又可方便地继承，能实现代码重用与扩充。

在派生类中，成员有4种不同的访问属性:

1.公用的，派生类内和派生类外都可以访问。

2.受保护的，派生类内可以访问，派生类外不能访问，其下一层的派生类可以访问。

3.私有的，派生类内可以访问，派生类外不能访问。

4.不可访问的，派生类内和派生类外都不能访问。


```cpp
#include <iostream>
#include <string>

using namespace std;

class Student                   //声明基类
{
public:                         //基类公用成员
void display( );
protected :                     //基类保护成员
	int num;
	string name;
	char sex;
};


void Student::display( )          //定义基类成员函数
{
	cout<<"num:"<<num<<endl;
	cout<<"name:"<<name<<endl;
	cout<<"sex:"<<sex<<endl;
}

class Student1: protected Student    //用protected方式声明派生类Student1
{
public:
	void display1( );                //派生类公用成员函数
private:
	int age;                         //派生类私有数据成员
	string addr;                     //派生类私有数据成员
};
void Student1::display1( )           //定义派生类公用成员函数
{
	cout<<"num:"<<num<<endl;         //引用基类的保护成员，合法
	cout<<"name:"<<name<<endl;       //引用基类的保护成员，合法
	cout<<"sex:"<<sex<<endl;         //引用基类的保护成员，合法
	cout<<"age:"<<age<<endl;         //引用派生类的私有成员，合法
	cout<<"address:"<<addr<<endl;    //引用派生类的私有成员，合法
}

int main()
{
	Student1 stud1;          //stud1是派生类Student1类的对象
	stud1.display1( );       //合法，display1是派生类中的公用成员函数
	stud1.num=10023;         //错误，外界不能访问保护成员
	return 0;
}
```

#### 多级派生时的访问属性

![](/images/posts/C++/108.png)

类A为基类，类B是类A的派生类，类C是类B的派生类，则类C也是类A的派生类。类B称为类A的直接派生类，类C称为类A的间接派生类。类A是类B的直接基类，是类C的间接基类。在多级派生的情况下，各成员的访问属性仍按以上原则确定。

```cpp
class A                 //基类
{
public:
	int i;
protected:
	void f2( );
	int j;
private:
	int k;
};

class B: public A       //public方式
{
public:
	void f3( );
	protected:
	void f4( );
private:
	int m;
};


class C: protected B    //protected方式
{
public:
	void f5( );
private:
	int n;
};
```

类A是类B的公用基类，类B是类C的保护基类。各成员在不同类中的访问属性如下：

![](/images/posts/C++/109.png)

无论哪一种继承方式，在派生类中是不能访问基类的私有成员的，私有成员只能被本类的成员函数所访问，毕竟派生类与基类不是同一个类。如果在多级派生时都采用公用继承方式，那么直到最后一级派生类都能访问基类的公用成员和保护成员。如果采用私有继承方式，经过若干次派生之后，基类的所有的成员已经变成不可访问的了。如果采用保护继承方式，在派生类外是无法访问派生类中的任何成员的。而且经过多次派生后，人们很难清楚地记住哪些成员可以访问，哪些成员不能访问，很容易出错。因此，在实际中，常用的是公用继承。


#### 简单的派生类的构造函数

用户在声明类时可以不定义构造函数，系统会自动设置一个默认的构造函数，在定义类对象时会自动调用这个默认的构造函数。这个构造函数实际上是一个空函数，不执行任何操作。如果需要对类中的数据成员初始化，应自己定义构造函数。构造函数的主要作用是对数据成员初始化。在设计派生类的构造函数时，不仅要考虑派生类所增加的数据成员的初始化，还应当考虑基类的数据成员初始化。也就是说，希望在执行派生类的构造函数时，使派生类的数据成员和基类的数据成员同时都被初始化。解决这个问题的思路是：在执行派生类的构造函数时，调用基类的构造函数。

任何派生类都包含基类的成员，简单的派生类只有一个基类，而且只有一级派生(只有直接派生类，没有间接派生类)，在派生类的数据成员中不包含基类的对象(即子对象)。

```cpp
#include <iostream>
#include<string>

using namespace std;

class Student{                          //声明基类Student
public:
	Student(int n,string nam,char s)    //基类构造函数
	{
		num=n;
		name=nam;
		sex=s;
	}
	~Student(){}                        //基类的析构函数
protected:                              //保护部分
	int num;
	string name;
	char sex ;
};

class Student1: public Student{         //声明派生类Student1
	public:                             //派生类的公用部分
	Student1(int n,string nam,char s,int a,string ad):Student(n,nam,s)  //派生类构造函数
	{
		age=a;                          //在函数体中只对派生类新增的数据成员初始化
		addr=ad;
	}
	void show( ){
		cout<<"num:"<<num<<endl;
		cout<<"name:"<<name<<endl;
		cout<<"sex:"<<sex<<endl;
		cout<<"age:"<<age<<endl;
		cout<<"address:"<<addr<<endl<<endl;
	}
	~Student1(){}                       //派生类的析构函数
private:                                //派生类的私有部分
	int age;
	string addr;
};


int main( )
{
	Student1 stud1(10010,"Wang-li",'f',19,"115 Beijing Road,Shanghai");
	Student1 stud2(10011,"Zhang-fun",'m',21,"213 Shanghai Road,Beijing");
	stud1.show( );                     //输出第一个学生的数据
	stud2.show( );                     //输出第二个学生的数据
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/112.png)

派生类构造函数首行的写法:

```cpp
Student1(int n,string nam,char s,int a,string ad):Student(n,nam,s)

其一般形式为：

派生类构造函数名（总参数表列）：基类构造函数名（参数表列）
{
	派生类中新增数据成员初始化语句
}
```

派生类构造函数将前面3个传递给基类构造函数的形参：

![](/images/posts/C++/113.png)

然后通过Student(n,nam,s)把3个值再传给基类构造函数的形参：

![](/images/posts/C++/114.png)

也可以将派生类构造函数在类外面定义，而在类体中只写该函数的声明：

```cpp
Student1(int n,string nam,char s,int a,string ad);
```

在类的外面定义派生类构造函数:

```cpp
Student1::Student1(int n,string nam,char s,int a，string ad):Student(n，nam，s)
{
	age=a;
	addr=ad;
}
```

请注意:**在类中对派生类构造函数作声明时，不包括基类构造函数名及其参数表列（即Student(n，nam，s)）**。只在定义函数时才将它列出。

调用基类构造函数时的实参是从派生类构造函数的总参数表中得到的，也可以不从派生类构造函数的总参数表中传递过来，而直接使用常量或全局变量。例如，派生类构造函数首行可以写成以下形式：

```cpp
Student1(string nam,char s,int a,string ad):Student(10010,nam,s)
```

即基类构造函数3个实参中，有一个是常量10010，另外两个从派生类构造函数的总参数表传递过来。

因此**不仅可以利用初始化表对构造函数的数据成员初始化，而且可以利用初始化表调用派生类的基类构造函数，实现对基类数据成员的初始化**。

在建立一个对象时，执行构造函数的顺序是：

1.派生类构造函数先调用基类构造函数；

2.再执行派生类构造函数本身(即派生类构造函数的函数体)。

对于上例来说，先初始化num，name，sex，然后再初始化age和addr。在派生类对象释放时，先执行派生类析构函数~Student1()，再执行其基类析构函数~Student()。

#### 有子对象的派生类的构造函数

类的数据成员中还可以包含类对象，如可以在声明一个类时包含这样的数据成员：

```cpp
Student s1;    //Student是已声明的类名，s1是Student类的对象
```

这时，s1就是类对象中的内嵌对象，称为子对象(subobject)，即对象中的对象。

```cpp
#include <iostream>
#include <string>

using namespace std;

class Student                      //声明基类
{
public:                            //公用部分
	Student(int n, string nam)     //基类构造函数
	{
		num=n;
		name=nam;
	}
	void display()                 //成员函数，输出基类数据成员
	{
		cout<<"num:"<<num<<endl<<"name:"<<name<<endl;
	}
protected:                         //保护部分
	int num;
	string name;
};


class Student1: public Student     //声明公用派生类Student1
{public:
	Student1(int n, string nam,int n1, string nam1,int a, string ad):Student(n,nam),monitor(n1,nam1)       //派生类构造函数
	{
		age=a;
		addr=ad;
	}

	void show(){
		cout<<"This student is:"<<endl;
		display();                                 //输出num和name
		cout<<"age:"<<age<<endl;                   //输出age
		cout<<"address:"<<addr<<endl<<endl;        //输出addr
	}

	void show_monitor( )                           //成员函数，输出子对象
	{
		cout<<endl<<"Class monitor is:"<<endl;
		monitor.display( );                        //调用基类成员函数
	}
private:                                           //派生类的私有数据
	Student monitor;                               //定义子对象(班长)
	int age;
	string addr;
};

int main()
{
	Student1 stud1(10010,"Wang-li",10001,"Li-sun",19,"115 BeijingRoad,Shanghai");
	stud1.show();                            //输出学生的数据
	stud1.show_monitor();                    //输出子对象的数据
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/115.png)


派生类构造函数的任务应该包括3个部分：

（1）对基类数据成员初始化；

（2）对子对象数据成员初始化；

（3）对派生类数据成员初始化。

程序中派生类构造函数首部如下：

```cpp
Student1(int n, string nam,int n1, string nam1,int a, string ad):
Student(n,nam),monitor(n1,nam1)      
```

![](/images/posts/C++/116.png)

定义派生类构造函数的一般形式为：

```cpp
派生类构造函数名（总参数表列）:基类构造函数名（参数表列），子对象名(参数表列)
{
	派生类中新增数成员据成员初始化语句
}
```

**执行派生类构造函数的顺序是：**

1.调用基类构造函数，对基类数据成员初始化；

2.调用子对象构造函数，对子对象数据成员初始化；

3.再执行派生类构造函数本身，对派生类数据成员初始化。

派生类构造函数的总参数表列中的参数，应当包括基类构造函数和子对象的参数表列中的参数。基类构造函数和子对象的次序可以是任意的，如上面的派生类构造函数首部可以写成：

```cpp
Student1(int n, string nam,int n1, string nam1,int a,string ad):
monitor(n1,nam1),Student(n,nam)
```

#### 多层派生时的构造函数

一个类不仅可以派生出一个派生类，派生类还可以继续派生，形成派生的层次结构。在上面叙述的基础上，不难写出在多级派生情况下派生类的构造函数。

```cpp
#include <iostream>
#include<string>

using namespace std;

class Student{                         //声明基类
public:                                //公用部分
	Student(int n, string nam)         //基类构造函数
	{
		num=n;
		name=nam;
	}
	void display()                     //输出基类数据成员
	{
		cout<<"num:"<<num<<endl;
		cout<<"name:"<<name<<endl;
	}
protected:                             //保护部分
	int num;                           //基类有两个数据成员
	string name;
};

class Student1: public Student{        //声明公用派生类Student1
public:
	Student1(int n,string nam,int a):Student(n,nam)   //派生类构造函数
	{
		age=a;                         //在此处只对派生类新增的数据成员初始化
	} 
	
	void show()                        //输出num，name和age
	{
		display();                     //输出num和name
		cout<<"age:"<<age<<endl;
	}
private:                               //派生类的私有数据
	int age;                           //增加一个数据成员
};

class Student2:public Student1{        //声明间接公用派生类Student2
public:                                //下面是间接派生类构造函数
	Student2(int n, string nam,int a,int s):Student1(n,nam,a)
	{
		score=s;
	}
	void show_all()                    //输出全部数据成员
	{
		show();                        //输出num和name
		cout<<"score:"<<score<<endl;   //输出age
	}
private:
	int score;                         //增加一个数据成员
};

int main( )
{
	Student2 stud(10010,"Li",17,89);
	stud.show_all();                   //输出学生的全部数据
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/117.png)

其派生关系为：

![](/images/posts/C++/118.png)

在声明Student2类对象时，调用Student2构造函数；在执行Student2构造函数时，先调用Student1构造函数；在执行Student1构造函数时，先调用基类Student构造函数。初始化的顺序是：

1.先初始化基类的数据成员num和name；

2.再初始化Student1的数据成员age；

3.最后再初始化Student2的数据成员score；

#### 派生类构造函数的特殊形式

当不需要对派生类新增的成员进行任何初始化操作时，派生类构造函数的函数体可以为空，即构造函数是空函数，如例子程序中派生类Student1构造函数可以改写为：

```cpp
Student1(int n,strin nam,int n1,string nam1):Student(n,nam),monitor(n1,nam1){}
```

此派生类构造函数的作用只是为了将参数传递给基类构造函数和子对象，并在执行派生类构造函数时调用基类构造函数和子对象构造函数。

**如果在基类中没有定义构造函数，或定义了没有参数的构造函数，那么在定义派生类构造函数时可不写基类构造函数。因为此时派生类构造函数没有向基类构造函数传递参数的任务。调用派生类构造函数时系统会自动首先调用基类的默认构造函数**。如果在基类和子对象类型的声明中都没有定义带参数的构造函数，而且也不需对派生类自己的数据成员初始化，则可以不必显式地定义派生类构造函数。因为此时派生类构造函数既没有向基类构造函数和子对象构造函数传递参数的任务，也没有对派生类数据成员初始化的任务。在建立派生类对象时，系统会自动调用系统提供的派生类的默认构造函数，并在执行派生类默认构造函数的过程中，调用基类
的默认构造函数和子对象类型默认构造函数。

**如果在基类或子对象类型的声明中定义了带参数的构造函数，那么就必须显式地定义派生类构造函数，并在派生类构造函数中写出基类或子对象类型的构造函数及其参数表**。

**如果在基类中既定义无参的构造函数，又定义了有参的构造函数(构造函数重载)，则在定义派生类构造函数时，既可以包含基类构造函数及其参数，也可以不包含基类构造函数**。在调用派生类构造函数时，根据构造函数的内容决定调用基类的有参的构造函数还是无参的构造函数。编程者可以根据派生类的需要决定采用哪一种方式。

#### 派生类的析构函数

在派生时，**派生类是不能继承基类的析构函数的，也需要通过派生类的析构函数去调用基类的析构函数**。在派生类中可以根据需要定义自己的析构函数，用来对派生类中所增加的成员进行清理工作。基类的清理工作仍然由基类的析构函数负责。在执行派生类的析构函数时，系统会自动调用基类的析构函数和子对象的析构函数，对基类和子对象进行清理。

**调用的顺序与构造函数正好相反：先执行派生类自己的析构函数，对派生类新增加的成员进行清理，然后调用子对象的析构函数，对子对象进行清理，最后调用基类的析构函数，对基类进行清理**。



