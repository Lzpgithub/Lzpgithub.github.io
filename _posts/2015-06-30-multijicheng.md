---
layout: post
title: 类的多重继承
categories: C和C++基础
description: 类的多重继承
keywords: 多重继承
---

#### 声明多重继承的方法

单继承，即一个类是从一个基类派生而来的。实际上，常常有这样的情况：一个派生类有两个或多个基类，派生类从两个或多个基类中继承所需的属性。C++为了适应这种情况，允许一个派生类同时继承多个基类。这种行为称为多重继承(multiple inheritance)。

如果已声明了类A、类B和类C，可以声明多重继承的派生类D:

```cpp
class D:public A,private B,protected C
{
	类D新增加的成员
}
```

D是多重继承的派生类，它以公用继承方式继承A类，以私有继承方式继承B类，以保护继承方式继承C类。D按不同的继承方式的规则继承A，B，C的属性，确定各基类的成员在派生类中的访问权限。

#### 多重继承派生类的构造函数

多重继承派生类的构造函数形式与单继承时的构造函数形式基本相同，只是在初始表中包含多个基类构造函数。如：

```cpp
派生类构造函数名(总参数表列):基类1构造函数(参数表列),
基类2构造函数(参数表列),基类3构造函数(参数表列){
	派生类中新增数成员据成员初始化语句
}
```

各基类的排列顺序任意。派生类构造函数的执行顺序同样为:先调用基类的构造函数，再执行派生类构造函数的函数体。**调用基类构造函数的顺序是按照声明派生类时基类出现的顺序**。

声明一个教师(Teacher)类和一个学生(Student)类，用多重继承的方式声明一个研究生(Graduate)派生类。教师类中包括数据成员name(姓名)、age(年龄)、title(职称)。学生类中包括数据成员name1(姓名)、age(性别)、score(成绩)。在定义派生类对象时给出初始化的数据，然后输出这些数据：

```cpp
#include <iostream>
#include <string>

using namespace std;

class Teacher{                             //声明类Teacher(教师)
public:                                    //公用部分
	Teacher(string nam,int a, string t)    //构造函数
	{
		name=nam;
		age=a;
		title=t;
	}
	void display()                         //输出教师有关数据
	{
		cout<<"name:"<<name<<endl;
		cout<<"age"<<age<<endl;
		cout<<"title:"<<title<<endl;
	}
protected:                                 //保护部分
	string name;
	int age;
	string title;                          //职称
};

class Student                              //定义类Student(学生)
{
public:
	Student(string nam,char s,float sco)   //构造函数
	{
		name1=nam;
		sex=s;
		score=sco;
	} 
	void display1()                        //输出学生有关数据
	{
		cout<<"name:"<<name1<<endl;
		cout<<"sex:"<<sex<<endl;
		cout<<"score:"<<score<<endl;
	}
protected:                                 //保护部分
	string name1;
	char sex;
	float score;                           //成绩
};

class Graduate:public Teacher,public Student{   //声明多重继承的派生类Graduate
public:
	Graduate(string nam,int a,char s, string t,float sco,float w):Teacher(nam,a,t),
		Student(nam,s,sco),wage(w){}

	void show( )                                //输出研究生的有关数据
	{
		cout<<"name:"<<name<<endl;
		cout<<"age:"<<age<<endl;
		cout<<"sex:"<<sex<<endl;
		cout<<"score:"<<score<<endl;
		cout<<"title:"<<title<<endl;
		cout<<"wages:"<<wage<<endl;
	}
private:
	float wage;                                 //工资
};


int main()
{
	Graduate grad1("Wang-li",24,'f',"assistant",89.5,1234.5);
	grad1.show();
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/120.png)

在两个基类中分别用name和name1来代表姓名，其实这是同一个人的名字，从Graduate类的构造函数中可以看到总参数表中的参数nam分别传递给两个基类的构造函数，作为基类构造函数的实参。

解决这个问题有一个好方法：在两个基类中可以都使用同一个数据成员名name，而在show函数中引用数据成员时指明其作用域，如：

```cpp
cout<<"name:"<<Teacher::name<<endl;
```

这就是惟一的，不致引起二义性，能通过编译，正常运行。

通过这个程序还可以发现一个问题：在多重继承时，从不同的基类中会继承一些重复的数据。如果有多个基类，问题会更突出。在设计派生类时要细致考虑其数据成员，尽量减少数据冗余。


#### 多重继承引起的二义性问题

多重继承可以反映现实生活中的情况，能够有效地处理一些较复杂的问题，使编写程序具有灵活性，但是多重继承也引起了一些值得注意的问题，它增加了程序的复杂度，使程序的编写和维护变得相对困难，容易出错。其中最常见的问题就是继承的成员同名而产生的二义性(ambiguous)问题。

如果类A和类B中都有成员函数display和数据成员a，类C是类A和类B的直接派生类。分别讨论下列3种情况:

**1.两个基类有同名成员**

![](/images/posts/C++/121.png)

```cpp
class A{
public:
	int a;
	void display();
};

class B{
public:
	int a;
	void display();
};

class C:public A,public B{
public :
	int b;
	void show();
};
```

如果在main函数中定义C类对象c1，并调用数据成员a和成员函数display：

```cpp
C c1;
c1.a=3;
c1.display();
```

由于基类A和基类B都有数据成员a和成员函数display，编译系统无法判别要访问的是哪一基类的成员，因此，程序编译出错。可以用基类名来限定：

```cpp
c1.A::a=3;         //引用c1对象中的基类A的数据成员a
c1.A::display();   //调用c1对象中的基类A的成员函数display
```

如果是在派生类C中通过派生类成员函数show访问基类A的display和a，可以不必写对象名而直接写：

```cpp
A::a=3;            //指当前对象
A::display();
```

![](/images/posts/C++/122.png)

**2.两个基类和派生类三者都有同名成员**

将上面的C类声明改为：

```cpp
class C:public A,public B
{
	int a;
	void display();
};
```

即有3个a，3个display函数：

![](/images/posts/C++/123.png)

如果在main函数中定义C类对象c1，并调用数据成员a和成员函数display：

```cpp
C c1;
c1.a=3;
c1.display();
```

程序能通过编译，也可正常运行。访问的是派生类C中的成员。规则是:**基类的同名成员在派生类中被屏蔽，成为“不可见”的，或者说，派生类新增加的同名成员覆盖了基类中的同名成员。因此如果在定义派生类对象的模块中通过对象名访问同名的成员，则访问的是派生类的成员**。

请注意：**不同的成员函数，只有在函数名和参数个数相同、类型相匹配的情况下才发生同名覆盖，如果只有函数名相同而参数不同，不会发生同名覆盖，而属于函数重载**。

要在派生类外访问基类A中的成员，应指明作用域A，写成以下形式：

```cpp
c1.A::a=3;             //表示是派生类对象c1中的基类A中的数据成员a
c1.A::display();       //表示是派生类对象c1中的基类A中的成员函数display
```

**3.如果类A和类B是从同一个基类派生的**

![](/images/posts/C++/124.png)

```cpp
#include <iostream>

using namespace std;

class N{
public:
	int a;
	void display(){
		cout<<"A::a="<<a<<endl;
	}
};

class A:public N{
public:
	int a1;
};

class B:public N{
public:
	int a2;
};

class C :public A,public B{
public :
	int a3;
	void show(){
		cout<<"a3="<<a3<<endl;
	}
};

int main()
{
	C c1;     //定义C类对象c1
	...
}
```

![](/images/posts/C++/125.png)


怎样才能访问类A中从基类N继承下来的成员呢？显然不能用：

```cpp
c1.a=3;
c1.display( );
或
c1.N::a=3; 
c1.N::display();
```

因为这样依然无法区别是类A中从基类N继承下来的成员，还是类B中从基类N继承下来的成员。应当通过类N的直接派生类名来指出要访问的是类N的哪一个派生类中的基类成员。如：

```cpp
c1.A::a=3; c1.A::display();    //要访问的是类N的派生类A中的基类成员
```

#### 虚基类的作用

如果一个派生类有多个直接基类，而这些直接基类又有一个共同的基类，则在最终的派生类中会保留该间接共同基类数据成员的多份同名成员：

![](/images/posts/C++/129.png)

在引用这些同名的成员时，必须在派生类对象名后增加直接基类名，以避免产生二义性，使其惟一地标识一个成员，如c1.A::display( )。在一个类中保留间接共同基类的多份同名成员，这种现象是人们不希望出现的。

C++提供虚基类(virtual base class)的方法，使得在继承间接共同基类时只保留一份成员。

将类A声明为虚基类：

```cpp
class A{                      //声明基类A
	...
};

class B :virtual public A{    //声明类B是类A的公用派生类，A是B的虚基类
	...
};

class C :virtual public A{    //声明类C是类A的公用派生类，A是C的虚基类
{
	...
};
```

注意:虚基类并不是在声明基类时声明的，而是在声明派生类时，指定继承方式时声明的。因为一个基类可以在生成一个派生类时作为虚基类，而在生成另一个派生类时不作为虚基类。声明虚基类的一般形式为：

```cpp
class 派生类名:virtual 继承方式 基类名
```

经过这样的声明后，当基类通过多条派生路径被一个派生类继承时，该派生类只继承该基类一次。

在派生类B和C中作了上面的虚基类声明后，派生类D中的成员如图：

![](/images/posts/C++/130.png)

为了保证虚基类在派生类中只继承一次，应当在该基类的所有直接派生类中声明为虚基类。否则仍然会出现对基类的多次继承。如下图中：

![](/images/posts/C++/131.png)

在派生类B和C中将类A声明为虚基类，而在派生类D中没有将类A声明为虚基类，则在派生类E中，虽然从类B和C路径派生的部分只保留一份基类成员，但从类D路径派生的部分还保留一份基类成员。

#### 虚基类的初始化

如果在虚基类中定义了带参数的构造函数，而且没有定义默认构造函数，则在其所有派生类(包括直接派生或间接派生的派生类)中，通过构造函数的初始化表对虚基类进行初始化。例如：

```cpp
class A                     //定义基类A
{
public:
	A(int i){}              //基类构造函数，有一个参数
	...
};

class B :virtual public A   //A作为B的虚基类
{
public:
	B(int n):A(n){}         //B类构造函数，在初始化表中对虚基类初始化
	...
};

class C :virtual public A   //A作为C的虚基类
{
public:
	C(int n):A(n){}         //C类构造函数，在初始化表中对虚基类初始化
	...
};

class D :public B,public C  //类D的构造函数，在初始化表中对所有基类初始化
{
public:
	D(int n):A(n),B(n),C(n){}
	...
};
```

在定义类D的构造函数时，与以往使用的方法有所不同。规定:**在最后的派生类中不仅要负责对其直接基类进行初始化，还要负责对虚基类初始化**。

**C++编译系统只执行最后的派生类对虚基类的构造函数的调用，而忽略虚基类的其他派生类(如类B和类C)对虚基类的构造函数的调用，这就保证了虚基类的数据成员不会被多次初始化**。

#### 虚基类的简单应用举例

在Teacher类和Student类之上增加一个共同的基类Person，作为人员的一些基本数据都放在Person中，在Teacher类和Student类中再增加一些必要的数据。

![](/images/posts/C++/132.png)

```cpp
#include <iostream>
#include <string>

using namespace std;

//声明公共基类Person
class Person{
public:
	Person(string nam,char s,int a)      //构造函数
	{
		name=nam;
		sex=s;
		age=a;
	}
protected:                               //保护成员
	string name;
	char sex;
	int age;
};

//声明Person的直接派生类Teacher
class Teacher:virtual public Person{     //声明Person为公用继承的虚基类
public:
	Teacher(string nam,char s,int a, string t):Person(nam,s,a)  //构造函数
	{
		title=t;
	}
protected:                                       //保护成员
	string title;                                //职称
};

//声明Person的直接派生类Student
class Student:virtual public Person{             //声明Person为公用继承的虚基类
public:
	Student(string nam,char s,int a,float sco)   //构造函数
	:Person(nam,s,a),score(sco){}                //初始化表
protected:                                       //保护成员
	float score;                                 //成绩
};

//声明多重继承的派生类Graduate
class Graduate:public Teacher,public Student     //Teacher和Student为直接基类
{
public:
	Graduate(string nam,char s,int a, string t,float sco,float w)       //构造函数
	:Person(nam,s,a),Teacher(nam,s,a,t),Student(nam,s,a,sco),wage(w){}  //初始化表
	void show()                                  //输出研究生的有关数据
	{
		cout<<"name:"<<name<<endl;
		cout<<"age:"<<age<<endl;
		cout<<"sex:"<<sex<<endl;
		cout<<"score:"<<score<<endl;
		cout<<"title:"<<title<<endl;
		cout<<"wages:"<<wage<<endl;
}
private:
	float wage;                                  //工资
};

//主函数
int main()
{
	Graduate grad1("Wang-li",'f',24,"assistant",89.5,1234.5);
	grad1.show();
	system("pause");
}
```

程序运行解雇为：

![](/images/posts/C++/133.png)

使用多重继承时要十分小心，经常会出现二义性问题。许多专业人员认为:不要提倡在程序中使用多重继承，只有在比较简单和不易出现二义性的情况或实在必要时才使用多重继承，能用单一继承解决的问题就不要使用多重继承。也是由于这个原因，有些面向对象的程序设计语言(如Java，Smalltalk)并不支持多重继承。


#### 虚基类内存分布

```cpp
#include <iostream>

using namespace std;

class A                     //定义基类A
{
public:
	A(int i){}              //基类构造函数，有一个参数
};

class B :virtual public A   //A作为B的虚基类
{
public:
	B(int n):A(n){}         //B类构造函数，在初始化表中对虚基类初始化
};

class C :virtual public A   //A作为C的虚基类
{
public:
	C(int n):A(n){}         //C类构造函数，在初始化表中对虚基类初始化
};

class D :public B,public C  //类D的构造函数，在初始化表中对所有基类初始化
{
public:
	D(int n):A(n),B(n),C(n){}
};
 
int main(){
	cout<<sizeof(A)<<endl;    //1  1个字节char
	cout<<sizeof(B)<<endl;    //4  1个虚基类指针
	cout<<sizeof(C)<<endl;    //4  1个虚基类指针
	cout<<sizeof(D)<<endl;    //8  2虚基类指针
	system("pause");
}
```

```cpp
#include <iostream>

using namespace std;

class A                     //定义基类A
{
public:
	A(int i){}              //基类构造函数，有一个参数
	int a;
};

class B :virtual public A   //A作为B的虚基类
{
public:
	B(int n):A(n){}         //B类构造函数，在初始化表中对虚基类初始化
	int b;
};

class C :virtual public A   //A作为C的虚基类
{
public:
	C(int n):A(n){}         //C类构造函数，在初始化表中对虚基类初始化
	int c;
};

class D :public B,public C  //类D的构造函数，在初始化表中对所有基类初始化
{
public:
	D(int n):A(n),B(n),C(n){}
	int d;
};
 
int main(){
	cout<<sizeof(A)<<endl;    //4   1个int成员变量
	cout<<sizeof(B)<<endl;    //12  2个int成员变量 1个虚基类指针
	cout<<sizeof(C)<<endl;    //12  2个int成员变量 1个虚基类指针
	cout<<sizeof(D)<<endl;    //24  4个int成员变量 2虚基类指针
	system("pause");
}
```

#### 基类与派生类的转换

只有公用派生类才是基类真正的子类型，它完整地继承了基类的功能。**基类与派生类对象之间有赋值兼容关系，由于派生类中包含从基类继承的成员，因此可以将派生类的值赋给基类对象，在用到基类对象的时候可以用其子类对象代替**。具体表现在以下几个方面:

**(1)派生类对象可以向基类对象赋值。**

可以用子类(即公用派生类)对象对其基类对象赋值。如：

```cpp
A a1;      //定义基类A对象a1
B b1;      //定义类A的公用派生类B的对象b1
a1=b1;     //用派生类B对象b1对基类对象a1赋值
```

**在赋值时舍弃派生类自己的成员**。实际上，所谓赋值只是对数据成员赋值，对成员函
数不存在赋值问题。

![](/images/posts/C++/134.png)

赋值后不能企图通过对象a1去访问派生类对象b1的成员，因为b1的成员与a1的成员是不同的。假设age是派生类B中增加的公用数据成员，分析下面的用法：

```cpp
a1.age=23;   //错误，a1中不包含派生类中增加的成员
b1.age=21;   //正确，b1中包含派生类中增加的成员
```

应当注意，子类型关系是单向的、不可逆的。B是A的子类型，不能说A是B的子类型。**只能用子类对象对其基类对象赋值，而不能用基类对象对其子类对象赋值，理由是显然的，因为基类对象不包含派生类的成员，无法对派生类的成员赋值。同理，同一基类的不同派生类对象之间也不能赋值**。

**(2)派生类对象可以替代基类对象向基类对象的引用进行赋值或初始化。**

如已定义了基类A对象a1，可以定义a1的引用变量：

```cpp
A a1;      //定义基类A对象a1
B b1;      //定义公用派生类B对象b1
A& r=a1;   //定义基类A对象的引用变量r，并用a1对其初始化
```

这时，引用变量r是a1的别名，r和a1共享同一段存储单元。也可以用子类对象初始化引用变量r，将上面最后一行改为：

```cpp
A& r=b1;  //定义基类A对象的引用变量r，并用派生类B对象b1对其初始化
```

此时r并不是b1的别名，也不与b1共享同一段存储单元。它只是b1中基类部分的别名，r与b1中基类部分共享同一段存储单元，r与b1具有相同的起始地址。

![](/images/posts/C++/135.png)

也可保留上面第3行“A& r=a1;”，而对r重新赋值：

```cpp
r=b1;     //用派生类B对象b1对a1的引用变量r赋值
```

**(3)如果函数的参数是基类对象或基类对象的引用，相应的实参可以用子类对象。**

如有一函数fun：

```cpp
void fun(A& r)          //形参是类A的对象的引用变量
{
	cout<<r.num<<endl;  //输出该引用变量的数据成员num
} 
```

函数的形参是类A的对象的引用变量，本来实参应该为A类的对象。由于子类对象与派生类对象赋值兼容，派生类对象能自动转换类型，在调用fun函数时可以用派生类B的对象b1作实参：

```cpp
fun(b1);
```

输出类B的对象b1的基类数据成员num的值。与前相同，在fun函数中只能输出派生类中基类成员的值。

**(4)派生类对象的地址可以赋给指向基类对象的指针变量，也就是说，指向基类对象的指针变量也可以指向派生类对象。**

定义一个基类Student(学生)，再定义Student类的公用派生类Graduate(研究生)，用指向基类对象的指针输出数据：

```cpp
#include <iostream>
#include <string>

using namespace std;

class Student                     //声明Student类
{
public:
	Student(int,string,float);    //声明构造函数
	void display();               //声明输出函数
private:
	int num;
	string name;
	float score;
};

Student::Student(int n, string nam,float s)    //定义构造函数
{
	num=n;
	name=nam;
	score=s;
}

void Student::display()                       //定义输出函数
{
	cout<<endl<<"num:"<<num<<endl;
	cout<<"name:"<<name<<endl;
	cout<<"score:"<<score<<endl;
}

class Graduate:public Student                //声明公用派生类Graduate
{
public:
	Graduate(int,string ,float,float);       //声明构造函数
	void display();                          //声明输出函数
private:
	float pay;                               //工资
};

Graduate::Graduate(int n, string nam,float s,float p):Student(n,nam,s),pay(p){}   //定义构造函数

void Graduate::display()                     //定义输出函数
{
	Student::display();                      //调用Student类的display函数
	cout<<"pay="<<pay<<endl;
}

int main()
{
	Student stud1(1001,"Li",87.5);           //定义Student类对象stud1
	Graduate grad1(2001,"Wang",98.5,563.5);  //定义Graduate类对象grad1
	Student *pt=&stud1;                      //定义指向Student类对象的指针并指向stud1
	pt->display();                           //调用stud1.display函数
	pt=&grad1;                               //指针指向grad1
	pt->display();                           //调用grad1.display函数
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/136.png)

很多读者认为：在派生类中有两个同名的display成员函数，根据同名覆盖的规则，被调用的应当是派生类Graduate对象的display函数，在执行Graduate::display函数过程中调用Student::display函数，输出num，name，score，然后再输出pay的值。事实上这种推论是错误的。

问题在于pt是指向Student类对象的指针变量，即使让它指向了grad1，但实际上pt指向的是grad1中从基类继承的部分。通过指向基类对象的指针，只能访问派生类中的基类成员，而不能访问派生类增加的成员。所以pt->display()调用的不是派生类Graduate对象所增加的display函数，而是基类的display函数，所以只输出研究生grad1的num,name,score3个数据。如果想通过指针输出研究生grad1的pay，可以另设一个指向派生类对象的指针变量ptr，使它指向grad1，然后用ptr->display()调用派生类对象的display函数，但这是很不方便的。

**用指向基类对象的指针变量指向子类对象是合法的、安全的，不会出现编译上的错误。但在应用上却不能完全满足人们的希望，人们有时希望通过使用基类指针能够调用基类和子类对象的成员。要解决这个问题，办法是使用虚函数和多态性**。

#### 继承在软件开发中的重要意义

有了继承，使软件的重用成为可能。继承是C++和C的最重要的区别之一。由于C++提供了继承的机制，这就吸引了许多厂商开发各类实用的类库。用户将它们作为基类去建立适合于自己的类(即派生类)，并在此基础上设计自己的应用程序。

类库的出现使得软件的重用更加方便，现在有一些类库是随着C++编译系统卖给用户的。读者不要认为类库是C++编译系统的一部分。不同的C++编译系统提供的由不同厂商开发的类库一般是不同的。

对类库中类的声明一般放在头文件中，类的实现(函数的定义部分)是单独编译的，以目标代码形式存放在系统某一目录下。用户使用类库时，不需要了解源代码，但必须知道头文件的使用方法和怎样去连接这些目标代码(在哪个子目录下)，以便源程序在编译后与之连接。

由于基类是单独编译的，在程序编译时只需对派生类新增的功能进行编译，这就大大提高了调试程序的效率。如果在必要时修改了基类，只要基类的公用接口不变，派生类不必修改，但基类需要重新编译，派生类也必须重新编译，否则不起作用。

(1)有许多基类是被程序的其他部分或其他程序使用的，这些程序要求保留原有的基类不受破坏。

(2)用户往往得不到基类的源代码。

(3)在类库中，一个基类可能已被指定与用户所需的多种组件建立了某种关系，因此在类库中的基类是不容许修改的。

(4)实际上，许多基类并不是从已有的其他程序中选取来的，而是专门作为基类设计的。

(5)在面向对象程序设计中，需要设计类的层次结构，从最初的抽象类出发，每一层派生类的建立都逐步地向着目标的具体实现前进。