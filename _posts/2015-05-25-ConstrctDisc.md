---
layout: post
title: 构造函数和析构函数
categories: C和C++基础
description: 构造函数和析构函数
keywords: 类，构造，析构
---

#### 初始化类的成员变量

在建立一个对象时，常常需要作某些初始化的工作，例如对数据成员赋初值。如果一个数据成员未被赋值，则它的值是不可预知的，因为在系统为它分配内存时，保留了这些存储单元的原状，这就成为了这些数据成员的初始值。类的数据成员是不能在声明类时初始化的。

如果一个类中所有的成员都是公用的，则可以在定义对象时对数据成员进行初始化。如：

```cpp
class Time
{public:             //声明为公用成员
	int hour;
	int minute;
	int sec;
};
Time t1={14,56,30};  //将t1初始化为14:56:30
```

这种情况和结构体变量的初始化是差不多的，在一个花括号内顺序列出各公用数据成员的值，两个值之间用逗号分隔。但是，如果数据成员是私有的，或者类中有private或protected的成员，就不能用种方法初始化。

#### 构造函数的作用

C++提供了构造函数(constructor)来处理对象的初始化。构造函数是一种特殊的成员函数，与其他成员函数不同，不需要用户来调用它，而是在建立对象时自动执行。构造函数的名字必须与类名同名，而不能由用户任意命名，以便编译系统能识别它并把它作为构造函数处理。它不具有任何类型，不返回任何值。构造函数的功能是由用户定义的，用户根据初始化的要求设计函数体和函数参数。

定义构造成员函数：

```cpp
#include <iostream>
using namespace std;
class Time
{public:
	Time()              //定义构造成员函数，函数名与类名相同
	{
		hour=0;         //利用构造函数对对象中的数据成员赋初值
		minute=0;
		sec=0;
	}
	void set_time( );   //函数声明
	void show_time( );  //函数声明
 private:
	int hour;           //私有数据成员
	int minute;
	int sec;
};

void Time::set_time( ) //定义成员函数，向数据成员赋值
{
	cin>>hour;
	cin>>minute;
	cin>>sec;
}

void Time::show_time( ) //定义成员函数，输出数据成员的值
{
	cout<<hour<<":"<<minute<<":"<<sec<<endl;
}

int main( )
{
	Time t1;            //建立对象t1，同时调用构造函数t1.Time( )
	t1.set_time( );     //对t1的数据成员赋值
	t1.show_time( );    //显示t1的数据成员的值
	Time t2;            //建立对象t2，同时调用构造函数t2.Time( )
	t2.show_time( );    //显示t2的数据成员的值
	return 0;
}
```

上面是在类内定义构造函数的，也可以只在类内对构造函数进行声明而在类外定义构造函数：

```cpp
Time( );       //对构造函数进行声明
```

在类外定义构造函数：

```cpp
Time::Time( ) //在类外定义构造成员函数，要加上类名Time和域限定符::”
{
	hour=0;
	minute=0;
	sec=0;
}
```

有关构造函数的使用:

1.在类对象进入其作用域时调用构造函数。

2.构造函数没有返回值，因此也不需要在定义构造函数时声明类型，这是它和一般函数的一个重要的不同之点。

3.构造函数不需用户调用，也不能被用户调用。

4.在构造函数的函数体中不仅可以对数据成员赋初值，而且可以包含其他语句。但是一般不提倡在构造函数中加入与初始化无关的内容，以保持程序的清晰。

5.如果用户自己没有定义构造函数，则C++系统会自动生成一个默认的构造函数，只是这个默认构造函数的函数体是空的，也没有参数，不执行初始化操作。


#### 带参数的构造函数

有时用户希望对不同的对象赋予不同的初值。可以采用带参数的构造函数，在调用不同对象的构造函数时，从外面将不同的数据传递给构造函数，以实现不同的初始化。构造函数首部的一般格式为：

```cpp
构造函数名(类型 1 形参1，类型2 形参2，...)
```

用户是不能调用构造函数的，因此无法采用常规的调用函数的方法给出实参。实参是在定义对象时给出的。定义对象的一般格式为：

```cpp
类名 对象名(实参1，实参2，...);
```

```cpp
#include <iostream>
using namespace std;
class Box
{public:
	Box(int,int,int);         //声明带参数的构造函数
	int volume( );            //声明计算体积的函数
 private:
	int height;
	int width;
	int length;
};

Box::Box(int h,int w,int len) //在类外定义带参数的构造函数
{
	height=h;
	width=w;
	length=len;
}

int Box::volume( )            //定义计算体积的函数
{
	return(height*width*length);
}

int main( )
{
	Box box1(12,25,30);          //建立对象box1，并指定box1长、宽、高的值
	cout<<"The volume of box1 is"<<box1.volume( )<<endl;
	Box box2(15,30,21);          //建立对象box2，并指定box2长、宽、高的值
	cout<<"The volume of box2 is"<<box2.volume( )<<endl;
	return 0;
}
```

由此可知：

1.带参数的构造函数中的形参，其对应的实参在定义对象时给定。

2.用这种方法可以方便地实现对不同的对象进行不同的初始化。

#### 用参数初始化表对数据成员初始化

C++还提供另一种初始化数据成员的方法——参数初始化表来实现对数据成员的初始化。这种方法不在函数体内对数据成员初始化，而是在函数首部实现。例如定义构造函数可以改用以下形式：

```cpp
Box::Box(int h,int w,int len):height(h),width(w),length(len){ }
```

这种写法方便、简练，尤其当需要初始化的数据成员较多时更显其优越性。甚至可以直接在类体中(而不是在类外)定义构造函数。

初始化列表的一个奇怪的特性：

初始化列表初始化参数时是按照声明的顺序初始化的，而不是按照出现在初始化列表中的顺序。

```cpp
#include <iostream>
using namespace std;
class Box
{public:
	Box(int);         //声明带参数的构造函数
	void show();            
 private:
	int height;
	int width;
	int length;
};

Box::Box(int i) //在类外定义带参数的构造函数
{
	length=++i;
	height=++i;
	width=++i;
}

void Box::show()          
{
	cout<<height<<" "<<width<<" "<<length<<endl;
}

int main()
{
	Box box1(0);  
	box1.show();
	system("pause");
}
```

输出结果为：

![](/images/posts/C++/14.png)

```cpp
#include <iostream>
using namespace std;
class Box
{public:
	Box(int);         //声明带参数的构造函数
	void show();            
 private:
	int height;
	int width;
	int length;
};

Box::Box(int i):length(++i),height(++i),width(++i) //在类外定义带参数的构造函数
{
}

void Box::show()          
{
	cout<<height<<" "<<width<<" "<<length<<endl;
}

int main()
{
	Box box1(0);  
	box1.show();
	system("pause");
}
```

输出结果为：

![](/images/posts/C++/15.png)


#### 构造函数的重载

在一个类中可以定义多个构造函数，以便对类对象提供不同的初始化的方法，供用户选用。这些构造函数具有相同的名字，而参数的个数或参数的类型不相同。这称为构造函数的重载。

```cpp
class Box
{public:
	Box();  //声明一个无参的构造函数
	//声明一个有参的构造函数，用参数的初始化表对数据成员初始化
	Box(int h,int w,int len):height(h),width(w),length(len){};
	int volume();
 private:
	int height;
	int width;
	int length;
};

Box::Box()  //定义一个无参的构造函数
{
	height=10;
	width=10;
	length=10;
}

int Box::volume()
{
	return(height*width*length);
}

int main()
{
	Box box1;            //建立对象box1,不指定实参
	cout<<"The volume of box1 is"<<box1.volume()<<endl;
	Box box2(15,30,25);  //建立对象box2,指定3个实参
	cout<<"The volume of box2 is"<<box2.volume()<<endl;
	return 0;
}
```

在本程序中定义了两个重载的构造函数，其实还可以定义其他重载构造函数，其原型声明可以为：

```cpp
Box::Box(int h);        //有1个参数的构造函数
Box::Box(int h,int w);  //有两个参数的构造函数
```

在建立对象时分别给定1个参数和2个参数。

总结：

1.调用构造函数时不必给出实参的构造函数，称为默认构造函数(default constructor)。显然，无参的构造函数属于默认构造函数。一个类只能有一个默认构造函数。

2.如果在建立对象时选用的是无参构造函数，应注意正确书写定义对象的语句。

3.尽管在一个类中可以包含多个构造函数，但是对于每一个对象来说，建立对象时只执行其中一个构造函数，并非每个构造函数都被执行。


#### 使用默认参数的构造函数

构造函数中参数的值既可以通过实参传递，也可以指定为某些默认值，即如果用户不指定实参值，编译系统就使形参取默认值。

```cpp
#include <iostream>
using namespace std;
class Box
{public:
	Box(int h=10,int w=10,int len=10); //在声明构造函数时指定默认参数
	int volume( );
private:
	int height;
	int width;
	int length;
};

Box::Box(int h,int w,int len) //在定义函数时可以不指定默认参数
{
	height=h;
	width=w;
	length=len;
}

int Box::volume( )
{
	return(height*width*length);
}

int main( )
{
	Box box1;             //没有给实参
	cout<<"The volume of box1 is"<<box1.volume( )<<endl;
	Box box2(15);         //只给定一个实参
	cout<<"The volume of box2 is"<<box2.volume( )<<endl;
	Box box3(15,30);      //只给定2个实参
	cout<<"The volume of box3 is"<<box3.volume( )<<endl;
	Box box4(15,30,20);   //给定3个实参
	cout<<"The volume of box4 is"<<box4.volume( )<<endl;
	return 0;
}
```

在构造函数中使用默认参数是方便而有效的，它提供了建立对象时的多种选择，它的作用相当于好几个重载的构造函数。它的好处是：即使在调用构造函数时没有提供实参值，不仅不会出错，而且还确保按照默认的参数值对对象进行初始化。尤其在希望对每一个对象都有同样的初始化状况时用这种方法更为方便。

总结：

1.应该在声明构造函数时指定默认值，而不能只在定义构造函数时指定默认值。

2.在声明构造函数时，形参名可以省略。

3.如果构造函数的全部参数都指定了默认值，则在定义对象时可以给一个或几个实参，也可以不给出实参。

4.在一个类中定义了全部是默认参数的构造函数后，不能再定义重载构造函数。


#### 析构函数

析构函数(destructor)也是一个特殊的成员函数，它的作用与构造函数相反，它的名字是类名的前面加一个“～”符号。在C++中“～”是位取反运算符，从这点也可以想到：析构函数是与构造函数作用相反的函数。当对象的生命期结束时，会自动执行析构函数。

具体地说如果出现以下几种情况，程序就会执行析构函数：

1.如果在一个函数中定义了一个对象(它是自动局部对象)，当这个函数被调用结束时，对象应该释放，在对象释放前自动执行析构函数；

2.static局部对象在函数调用结束时对象并不释放，因此也不调用析构函数，只在main函数结束或调用exit函数结束时，才调用static局部对象的析构函数；

3.如果定义了一个全局对象，则在程序的流程离开其作用域时(如main函数结束或调用exit函数)时，调用该全局对象的析构函数。

4.如果用new运算符动态地建立了一个对象，当用delete运算符释放该对象时，先调用该对象的析构函数。

析构函数的作用并不是删除对象，而是在撤销对象占用的内存之前完成一些清理工作，使这部分内存可以被程序分配给新对象使用。程序设计者事先设计好析构函数，以完成所需的功能，只要对象的生命期结束，程序就自动执行析构函数来完成这些工作。

析构函数不返回任何值，没有函数类型，也没有函数参数。因此它不能被重载。一个类可以有多个构造函数，但只能有一个析构函数。

析构函数的作用并不仅限于释放资源方面，它还可以被用来执行“用户希望在最后一次使用对象之后所执行的任何操作”，例如输出有关的信息。这里说的用户是指类的设计者，因为，析构函数是在声明类的时候定义的。也就是说，析构函数可以完成类的设计者所指定的任何操作。

一般情况下，类的设计者应当在声明类的同时定义析构函数，以指定如何完成“清理”的工作。如果用户没有定义析构函数，C++编译系统会自动生成一个析构函数，但它只是徒有析构函数的名称和形式，实际上什么操作都不进行。想让析构函数完成任何工作，都必须在定义的析构函数中指定。


#### 调用构造函数和析构函数的顺序

在使用构造函数和析构函数时，需要特别注意对它们的调用时间和调用顺序。

在一般情况下，调用析构函数的次序正好与调用构造函数的次序相反：最先被调用的构造函数，其对应的(同一对象中的)析构函数最后被调用，而最后被调用的构造函数，其对应的析构函数最先被调用：

![](/images/posts/C++/17.png)


##### 例子1：

```cpp
#include <iostream>

using namespace std;

class A
{
public:
    A(){
		cout<<"Construct A"<<endl;
	}

    ~A(){
		cout<<"DisConstruct A"<<endl;
	}
};

class B
{
public:
    B(){
		cout<<"Construct B"<<endl;
	}

    ~B(){
		cout<<"DisConstruct B"<<endl;
	}

    A a;
};

int main()
{
	B b;       //会自动执行B的相关构造函数
	return 0;
}
```

运行结果：

![](/images/posts/C++/18.png)

由于类B中的成员a是自定义类型的类A对象，在执行B的构造函数前要执行A的构造函数对a进行初始化。


##### 例子2：

```cpp
#include <iostream>

using namespace std;

class A
{
public:
    A(){
		cout<<"Construct A"<<endl;
	}

    ~A(){
		cout<<"DisConstruct A"<<endl;
	}
};

class B
{
public:
    B(){
		cout<<"Construct B"<<endl;
	}

    ~B(){
		cout<<"DisConstruct B"<<endl;
	}
};

class C
{
public:
    C(){
		cout<<"Construct C"<<endl;
	}

    ~C(){
		cout<<"DisConstruct C"<<endl;
	}
};

int main()
{
	A a;   //会自动执行A的相关构造函数
	B b;   //会自动执行B的相关构造函数
	C c;   //会自动执行C的相关构造函数
	return 0;
}

```

运行结果：

![](/images/posts/C++/19.png)


##### 例子3

```cpp
#include<string>
#include<iostream>
using namespace std;
class Student //声明Student类
{
 public:
	student(int n,string nam,char s )        //定义构造函数
	{
		num=n;
		name=nam;
		sex=s;
		cout<<"Constructor called."<<endl;   //输出有关信息
	}

	~Student()                              //定义析构函数
	{
		cout<<"Destructor called."<<endl;    //输出有关信息
	}   
                                 
	void display()                          //定义成员函数
	{
		cout<<"num:"<<num<<endl;
		cout<<"name:"<<name<<endl;
		cout<<"sex:"<<sex<<endl<<endl; 
	}

 private:
	int num;
	char name[10];
	char sex;
};


int main()
{
	Student stud1(10010,"Wang_li",'f');      //建立对象stud1
	stud1.display();                        //输出学生1的数据
	Student stud2(10011,"Zhang_fun",'m');    //定义对象stud2
	stud2.display();                        //输出学生2的数据
	return 0;
}
```

程序运行过程：

![](/images/posts/C++/16.png)


总结：

1.在全局范围中定义的对象(即在所有函数之外定义的对象)，它的构造函数在文件中的所有函数(包括main()函数)，执行之前调用。但如果一个程序中有多个文件，而不同的文件中都定义了全局对象，则这些对象的构造函数的执行顺序是不确定的。当main函数执行完毕或调用exit函数时(此时程序终止)，调用析构函数。

2.如果定义的是局部自动对象(例如在函数中定义对象)，则在建立对象时调用其构造函数。如果函数被多次调用，则在每次建立对象时都要调用构造函数。在函数调用结束、对象释放时先调用析构函数。

3.如果在函数中定义静态(static)局部对象，则只在程序第一次调用此函数建立对象时调用构造函数一次，在调用结束时对象并不释放，因此也不调用析构函数，只在mian函数结束或调用exit函数结束程序时，才调用析构函数。


#### 析构函数的作用

析构函数是提供一个在对象删除前可以释放这个对象所占有的资源的机会。比如：

```cpp
class A
{
    A(){m_a=new int[10];}
    ~A(){delete [] m_a;}

    int *m_a;
};
```

可以看到，析构函数提供了一个释放m_a所占用的内存的机会——当然释放的动作还需要你使用delete来做到。这就是所谓的"在撤销对象占有的内存之前完成一些清理工作"的情形之一,还有就是再复杂一点的情况，请看下面例子：

```cpp
class B
{
    B(){}
    ~B(){}

    A a;
}
```

构造函数B()会自动调用A()函数来做A.m_a的初始化工作；而析构函数~B()会自动调用A的析构函数~A()做清除内存的动作。

