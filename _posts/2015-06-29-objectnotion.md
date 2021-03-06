---
layout: post
title: 面向对象的基本概念
categories: C和C++基础
description: 面向对象的基本概念
keywords: 对象，概念
---

传统的过程式编程语言以过程为中心、以算法为驱动，而面向对象的编程语言则是以对象为中心，以消息为驱动。用公式表示，过程式编程语言为：程序=算法+数据。面向对象编程语言为：程序=对象+消息。

#### 1.面向对象的基本概念是什么？

类、对象和消息，类的基本特点：抽象、封装、继承和多态。

#### 4.class和struct有什么区别？

C是一种过程化语言，struct作为一种复杂数据类型定义，只能定义成员变量，不能定义成员函数。例如下面的C程序片段：

```cpp
struct Point{
	int x;   //合法
	int y;   //合法
	void print(){
		printf("Point print\n");   //编译错误 不能定义成员函数
	}
}
```

```cpp
#include <iostream>
using namespace std;

class CPoint{
	int x;           //默认为private
	int y;
	void print(){
		cout<<"CPoint:("<<x<<","<<y<<")"<<endl;
	}
public:
	CPoint(int x,int y){    //构造函数
		this->x=x;
		this->y=y;
	}
	void print1(){
		cout<<"CPoint:("<<x<<","<<y<<")"<<endl;
	}
};

struct SPoint{
	int x;           //默认为public
	int y;
	void print(){
		cout<<"SPoint:("<<x<<","<<y<<")"<<endl;
	}
	SPoint(int x,int y){    //构造函数
		this->x=x;
		this->y=y;
	}
private:
	void print1(){
		cout<<"SPoint:("<<x<<","<<y<<")"<<endl;
	}
};

int main(){
	CPoint cpt(1,2);    //调用CPoint带参数的构造函数
	SPoint spt(3,4);    //调用SPoint带参数的构造函数

	cout<<cpt.x<<" "<<cpt.y<<endl;   //编译错误
	cpt.print();        //编译错误
	cpt.print1();       //合法

	spt.print();        //合法
	spt.print1();       //编译出错
	cout<<spt.x<<" "<<spt.y<<endl;   //合法
	return 0;
}
```

程序解析：main函数的编译错误全部是因为访问private成员而产生的，因此可以看到class中默认的成员访问权限是private的，而struct中则是public。

```cpp
#include <iostream>

using namespace std;

class CBase{
public:
	void print(){                //public成员函数
		cout<<"CBase:print()..."<<endl;
	}
};

class CDerived1:CBase{           //默认private继承
};

class CDerived2:public CBase{    //指定public继承
};

struct SDerived1:CBase{           //默认public继承
};

struct SDerived2:private CBase{   //指定private继承
};

int main(){
	CDerived1 cd1;
	CDerived2 cd2;
	SDerived1 sd1;
	SDerived2 sd2;

	cd1.print();      //编译错误
	cd2.print();
	sd1.print();
	sd2.print();      //编译错误
}
```

可以看出，以private方式继承父类的子类对象不能访问父类的public成员。class默认是private继承，而struct继承默认是public继承。

总结：

1.C的struct与C++的class的区别：struct只是作为一种复杂数据类型定义，不能用于面向对象编程。

2.C++中的struct和class的区别：对于成员访问权限以及继承方式，class中默认的是private，而struct中则是public。class还可以用于表达模板类型，struct则不行。

#### 6.C++成员访问

请指出下面程序中标记为（1）、（2）、（3）的代码哪一个是错误的。

```cpp
#include <iostream>
#define public private       //(1)

using namespace std;

class Animal{
public:                      //(2)
	void MakeNoise();
};

int main(){
	Animal animal;
	animal.MakeNoise();      //(3)
	return 0;
}
```

程序解析：

（1）.正确，把public宏定义为private；

（2）.错误，成员函数只声明而没有定义；

（3）.错误，成员函数是私有不能再类外访问；

#### 7.类成员的初始化

```cpp
#include <iostream>

using namespace std;

class Obj{
public:
	Obj(int k):j(k),i(j){}

	void print(void){
		cout<<i<<endl<<j<<endl;
	}
private:
	int i;
	int j;
};

int main(){
	Obj obj(2);
	obj.print();
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/111.png)

初始化列表的初始化顺序与变量声明的顺序一致，而不是按照出现在初始化列表中的顺序。

#### 8.分析代码写结果--静态成员变量的使用

```cpp
#include <iostream>

using namespace std;

class Myclass{
public:
	Myclass(int a,int b,int c);
	void GetNumber();
	void GetSum();
private:
	int A;
	int B;
	int C;
	int Num;
	static int Sum;
};

int Myclass::Sum=0;

Myclass::Myclass(int a,int b,int c){
	A=a;
	B=b;
	C=c;
	Num=A+B+C;
	Sum=A+B+C;
}

void Myclass::GetNumber(){
	cout<<"Number="<<Num<<endl;
}

void Myclass::GetSum(){
	cout<<"Sum="<<Sum<<endl;
}

int main(){
	Myclass M(3,7,10);
	Myclass N(14,9,11);
	M.GetNumber();
	N.GetNumber();
	M.GetSum();
	N.GetSum();
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/119.png)

#### 与全局对象相比，使用静态数据成员有什么优势？

1.静态数据成员没有进入程序的全局命名空间，因此不存在程序中其他全局名字冲突的问题；

2.使用静态数据成员可以隐蔽信息，因为静态成员可以是private成员，而全局对象不能。

静态成员如果有n个同类的对象，那么每一个对象都分别有自己的数据成员，不同对象的数据成员各自有值，互不相干。但是有时人们希望有某一个或几个数据成员为所有对象所共有。这样可以实现数据共享。

在前面介绍过全局变量能够实现数据共享。如果在一个程序文件中有多个函数，在每一个函数中都可以改变全局变量的值，全局变量的值为各函数共享。但是用全局变量的安全性得不到保证，由于在各处都可以自由地修改全局变量的值，很有可能偶一失误，全局变量的值就被修改，导致程序的失败。因此在实际工作中很少使用全局变量。

如果想在同类的多个对象之间实现数据共享，也不要用全局对象，可以用静态的数据成员。


#### 10.有哪几种情况只能用初始化列表，而不能用赋值？

解析：

无论是在构造函数初始化列表中初始化成员，还是在构造函数体中对它们赋值，最终结果都是相同的。不同之处在于，构造函数在初始化列表初始化数据成员，没有定义初始化列表的构造函数在构造函数体中对数据成员赋值。

一种情况是：**const和引用类型数据成员变量只能被初始化而不能赋值操作**。

另一种情况是：**类的构造函数需要调用其基类的构造函数**。

```cpp
#include <iostream>

using namespace std;

class A{              //A是父类
private:
	int a;            //private成员
public:
	A(){}
	A(int x):a(x){}   //带参数的构造函数对a初始化
	void printA(){    //打印a的值
		cout<<"a="<<a<<endl;
	}
};

class B:public A{
private:
	int b;
public:
	B(int x,int y):A(x){    //需要初始化b以及父类a
		a=x;                //a为private 无法在子类被访问 编译出错
		A(x);               //调用方式错误 编译错误
		b=y;
	}
	void printB(){          //打印b的值
		cout<<"b="<<b<<endl;
	}
};

int main(){
	B b(2,3);
	b.printA();
	b.printB();
	return 0;
}
```

#### 11.代码改错--静态成员的使用

```cpp
#include <iostream>

using namespace std;

class test{
public:
	static int i;             //i没有初始化
	int j;
	test(int a):i(1),j(a){}
	void func1();
	static void func2();
};

void test::func1(){cout<<i<<","<<j<<endl;
void test::func2(){cout<<i<<","<<j<<endl;  //静态成员函数非法调用非静态成员变量j

int main(){
	test t(2);
	t.func1();
	t.func2();
	return 0;
}
```

#### 14.C++中的空类，默认情况下会产生哪些类成员函数

解析：

对于一个C++的空类，例如Empty：

```cpp
class Empty{
};
```

虽然Empty类定义中没有任何成员，但为了进行一些默认的操作，编译器会加入以下成员函数，这些成员函数使得类的对象拥有一些通用的功能。

1.默认构造函数和复制构造函数，它们被用于类对象的构造过程；

2.析构函数，它被用于同类的对象的析构过程；

3.赋值函数，它被用于同类的对象间的赋值过程；

4.取值运算，当对类的对象进行取地址（&）时，此函数被调用；

由上可知即使程序没有定义类的任何成员，编译器也会插入一些函数，完整的Empty类定义如下：

```cpp
class Empty{
public:
	Empty();                         //默认构造函数
	Empty(const Empty&);             //复制构造函数
	~Empty();                        //析构函数
	Empty& operator=(const Empty&);  //赋值运算符
	Empty* operator&();              //取址运算符
	const Empty* operator&() const;  //取址运算符
}
```

#### 15.构造函数和析构函数是否可以被重载？

答：

构造函数可以被重载，因为构造函数可以有多个，而且可以带参数；

析构函数不可以被重载，因为析构函数只能有一个，并且不能带参数；

#### 16.分析代码--重载构造函数的使用

```cpp
class Test{
public:
	Test(){}
	Test(char *Name,int len=0){}
	Test(char *Name){}
};

int main(){
	Test obj("Hello");
	return 0;
}
```

解析：Test类定义了三个构造函数，构造函数中使用了重载又使用默认参数，因此由于构造函数的模糊语义，编译器无法决定调用哪一个构造函数，会产生编译错误。


#### 17.分析代码--构造函数的使用

```cpp
#include <iostream>

using namespace std;

struct CLS{
	int m_i;
	CLS(int i):m_i(i){
		cout<<"create obj"<<m_i<<endl;
	}
};

int main(){
	CLS(1);                 //创建一个对象 不过没有保存
	CLS obj2=CLS(2);        //创建一个对象 保存到obj2
	CLS obj3(3);            //创建一个对象 保存到obj3
	CLS *obj4=new CLS(4);   //创建一个对象 保存到指针obj4
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/126.png)

以下代码中输出语句会输出0吗？为什么？

```cpp
#include <iostream>

using namespace std;

struct CLS{
	int m_i;
	CLS(int i):m_i(i){}
	CLS(){
		CLS(0);     //#1 相当于在构造函数中创建了一个对象 不过对象没有保存
	}
};

int main(){
	CLS obj;      
	cout<<obj.m_i<<endl;  //输出随机值
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/127.png)

1号行相当于在无参构造函数中调用有参构造函数创建了一个对象 不过对象没有保存，因此输出为一个随机值。

我们可以增加以下代码来验证：

```cpp
#include <iostream>

using namespace std;

struct CLS{
	int m_i;
	CLS(int i):m_i(i){
		cout<<"CLS():this="<<this<<endl;  //打印当前对象地址
	}
	CLS(){
		CLS(0);                           //#1
		cout<<"CLS():this="<<this<<endl;  //打印当前对象地址
	}
};

int main(){
	CLS obj;      
	cout<<"&boj="<<&obj<<endl;            //输出创建对象的地址
	cout<<obj.m_i<<endl;                  //输出随机值
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/128.png)

可以看到，在带参数的构造函数里打印出来的对象地址和对象obj的地址并不一致。实际上1号行的调用只是在栈上生成一个临时对象，对于自己毫无影响。还可以发现，构造函数的互相调用引起的后果不是死循环，而是栈溢出。

#### 18.构造函数explicit与普通构造函数的区别

```cpp
#include <iostream>

using namespace std;

class Test1{
public:
	Test1(int n){num=n;}       //普通构造函数
private:
	int num;
};

class Test2{
public:
	explicit Test2(int n){num=n;}   //explicit （显式）构造函数
private:
	int num;
};

int main(){
	Test1 t1=12;        //#1隐式调用其构造函数 正确
	Test2 t2=12;        //#2编译错误 不能隐式调用其构造函数
	Test2 t3(12);       //显式调用 正确
	system("pause");
}
```

Test1的构造函数带一个int型的参数，代码1号行会隐式转换成调用Test1的构造函数，Test的构造函数被声明为explicit，这表示不能通过隐式转换来调用这个构造函数，因此2号行会出现编译错误。

```cpp
#include <iostream>
#include <string>

using namespace std;

class A{
public:
	A(int p):a(p){}
	int a;
};

void Show(A aobj){
	cout<<aobj.a<<endl;
}

int main(){
	A a1=1;            //隐式调用构造函数
	cout<<a1.a<<endl;

	int num=2;
	Show(num);         //创建实参进行隐式调用构造函数
	system("pause");
}
```

输出结果为1和2.

#### 19.分析代码--explicit构造函数的作用

下面程序的函数show()被调用时，输出结果是什么？

```cpp
#include <iostream>
#include <string>

using namespace std;

class Number{
public:
	string type;
	Number():type("void"){}
	explicit Number(short):type("short"){}
	Number(int):type("int"){};
};

void Show(const Number& n){
	cout<<n.type<<endl;
}

int main(){
	short s=42;
	Show(s);
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/145.png)

Show()函数的参数类型是Number类对象的引用，在调用Show(n)时采取了以下所示步骤：

1.Show(s)中s为short类型，其值为42,因此首先检查参数为short的构造函数能否被隐式转换，由于short的构造函数被声明为显示调用，因此不能隐式转换。于是进入下一步操作。

2.数值42自动转换为int型。

3.检查参数为int的构造函数能否被隐式转换，由于代码中int的构造函数没有被声明为显示调用，因此调用此构造函数构造出一个临时对象。

4.打印上一步临时对象的type成员。


#### 20.C++中虚析构函数的作用是什么？

```cpp
#include <iostream>
using namespace std;

class Base{
public:
	Base(){};               //Base的构造函数
	~Base(){                //Base的析构函数
		cout<<"Output from the destructor of class Base!"<<endl;
	}
	virtual void DoSomthing(){
		cout<<"Do something in class Base!"<<endl;
	}
};

class Derived:public Base{  //基类Base的构造函数一定要声明为公有成员 否则派生类无法调用
public:
	Derived(){}             //Derived的构造函数
	~Derived(){
		cout<<"Output from the destructor of class Derived!"<<endl;
	}
	void DoSomthing(){
		cout<<"Do something in class Derived!"<<endl;
	}
};

int main(){
	Derived *pTest1=new Derived();    //Derived类的指针
	pTest1->DoSomthing();
	delete pTest1;

	cout<<endl;
	
	Base *pTest2=new Derived();
	pTest2->DoSomthing();
	delete pTest2;
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/146.png)

可以看出代码释放pTest1的资源，但并不能释放pTest2的资源，因为从结果可以看出Derived类的析构函数并没有被调用。通常情况下类的析构函数里面都是释放内存资源，而析构函数不被调用就会造成内存泄漏。原因是指针pTest2是Base类型的指针，释放pTest2时只进行Base类的析构函数。如果在Base的析构函数前增加virtual关键字，则：

![](/images/posts/C++/147.png)

#### 21.分析代码写结果--析构函数的执行顺序

```cpp
#include <iostream>

using namespace std;

class A{
private:
	int a;
public:
	A(int aa){a=aa;}
	~A(){cout<<"Destructor A!"<<a<<endl;}
};

class B:public A{
private:
	int b;
public:
	B(int aa=0,int bb=0):A(aa){b=bb;}
	~B(){cout<<"Destructor B!"<<b<<endl;}
};

void zhixing(){
	B obj(5),obj2(6,7);
}

int main(){
	zhixing();
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/148.png)

main()函数中定义了两个类B的对象，它们的基类是A。由于这两个对象都是栈中分配的，当main()函数退出时会发生析构，又因为obj1比obj2先声明，所以obj2先析构，它们析构的顺序是首先执行B的析构函数，然后再执行A的析构函数。


#### 22.复制构造函数是什么？什么情况下会用到它？什么是深复制和浅复制？

复制构造函数是一种特殊的构造函数，它由编译器调用来完成一些基于同一类的其他对象的构造及初始化。

如果在类中没有显式声明一个复制构造函数，那么，编译器会私下里指定一个函数来进行对象之间的位复制，这个隐含的构造函数简单地关联了所有的类成员。

在C++中，3中对象需要复制，此时复制构造函数将会被调用：

1.一个对象以值传递的方式传入函数体；

2.一个对象以值传递的方式从函数返回；

3.一个对象需要通过另一个对象进行初始化。

```cpp
#include <iostream>

using namespace std;

class Test{
public:
	int a;
	Test(int x){
		a=x;
	}
	Test(Test &test){
		cout<<"copy constructor"<<endl;
		a=test.a;
	}
};

void fun1(Test test){               //(1)值传递入函数体
	cout<<"func()..."<<endl;
}
 
Test fun2(){                        //(2)值传递从函数体返回
	Test t(2);
	cout<<"fun2()"<<endl;
	return t;
}

int main(){
	Test t1(1);
	Test t2=t1;                     //(3)用t1对t2做初始化
	cout<<"before fun1()..."<<endl;

	fun1(t1);

	Test t3=fun2();
	cout<<"after fun2()..."<<endl;

	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/149.png)


深复制和浅复制：

```cpp
#include <iostream>

using namespace std;

class Test{
public:
	char *buf;
	Test(void){                        //不带参数的构造函数
		buf=NULL;
	}
	Test(const char *str){             //带参数的构造函数
		buf=new char[strlen(str)+1];   //分配堆内存
		strcpy(buf,str);               //复制字符串
	}
	~Test(){
		if(buf!=NULL){
			delete buf;                //释放buf指向的堆内存
			buf=NULL;
		}
	}
};

int main(){
	Test t1("hello");
	Test t2=t1;         //调用默认的复制构造函数
	cout<<"(t1.buf==t2.buf)?："<<(t1.buf==t2.buf?"yes":"no")<<endl;
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/150.png)

程序退出的时候会崩溃，原因在于t1对象中的buf和t2对象中的buf指向同一地址，退出的时候进行两次析构，释放同一块内存。这就是浅复制。下面是深复制代码：

```cpp
#include <iostream>

using namespace std;

class Test{
public:
	char *buf;
	Test(void){                        //不带参数的构造函数
		buf=NULL;
	}
	Test(const char *str){             //带参数的构造函数
		buf=new char[strlen(str)+1];   //分配堆内存
		strcpy(buf,str);               //复制字符串
	}
	Test(Test &test){
		buf=new char[strlen(test.buf)+1];
		strcpy(buf,test.buf);
	}
	~Test(){
		if(buf!=NULL){
			delete buf;                //释放buf指向的堆内存
			buf=NULL;
		}
	}
};

int main(){
	Test t1("hello");
	Test t2=t1;         //调用默认的复制构造函数
	cout<<"(t1.buf==t2.buf)?："<<(t1.buf==t2.buf?"yes":"no")<<endl;
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/151.png)

复制构造函数是一种特殊的构造函数，它由编译器调用来完成一些基于同一类的其他对象的构造及初始化。

浅复制是让新旧两个对象指向同一个外部的内容，而深复制是指为新对象制作了外部对象的独立复制。

#### 23.什么时候编译器会产生默认的copy constructor呢？如果已经写了一个构造函数，编译器还会生成copy constructor吗？

答：如果用户没有自定义复制构造函数，并且在代码中使用到了复制构造函数，编译器就会生成默认的复制构造函数；但是如果用户定义了复制构造函数，编译器就不会再产生复制构造函数。

如果用户定义了一个构造函数，但不是复制构造函数，而此时在代码中又用到了复制构造函数，编译器也还会生成默认的复制构造函数。


#### 24.写一个继承类的复制函数

如果基类没有私有成员，即所有成员都能被派生类访问，则派生类的复制构造函数很容易编写。但如果基类有私有成员，并且这些私有成员必须在调用派生类的复制构造函数时初始化。方法：使用基类的复制构造函数。

```cpp
#include <iostream>

using namespace std;

class Base{
public:
	Base():i(0){cout<<"Base()"<<endl;}           //默认普通构造函数
	Base(int n):i(n){cout<<"Base(int)"<<endl;}   //普通构造函数
	Base(const Base &b):i(b.i){                  //复制构造函数
		cout<<"Base(Base&)"<<endl;
	}
private:
	int i;                                       //私有成员
};

class Derived:public Base{
public:
	Derived():Base(0),j(0){cout<<"Derived()"<<endl;}    //默认普通构造函数
	Derived(int m,int n):Base(m),j(n){cout<<"Derived(int,int)"<<endl;}   //普通构造函数
	Derived(Derived &obj):Base(obj),j(obj.j){           //调用Derived类的复制构造函数
		cout<<"Derived(Derived&)"<<endl;                //调用了Base的复制构造函数
	}
private:
	int j;
};

int main(){
	Base b(1);
	Derived obj(2,3);
	cout<<"------------------"<<endl;
	Derived d(obj);                      //调用Derived的复制构造函数
	cout<<"------------------"<<endl;
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/152.png)

#### 25.复制构造函数和赋值构造函数有什么区别？

复制构造和赋值函数有以下三个区别：

1.复制构造是一个对象初始化一块内存区域，这块内存就是新对象的内存区：

```cpp
class A;
A a;
A b=a;    //复制构造函数调用
A b(a);   
```

而赋值函数是对于一个已经被初始化的对象来进行赋值操作。例如：

```cpp
class A;
A a;
A b;
b=a;       //赋值函数调用
```

2.一般来说在数据成员包含指针对象的时候，应该考虑两种不同的处理需求：一种是复制指针对象，另一种是引用指针对象。**复制构造函数大多数情况下是复制，赋值函数则是引用对象**。

3.实现不一样。复制构造函数首先是一个构造函数，它调用的时候是通过参数对象初始化产生一个对象。赋值函数则是把一个新的对象赋值给一个原有的对象，所以原来的对象中有内存分配要先把内存释放掉，而且还要检查一下两个对象是不是同一个对象，如果是，则不做任何操作。


#### 26.编写类String的构造函数、析构函数和赋值函数

```cpp
#include <iostream>
using namespace std;

class mString{
public:
	mString(const char *str=NULL);      //普通构造函数
	mString(const mString &other);      //复制构造函数
	~mString();                         //析构函数
	mString& operator=(const mString &other);  //赋值函数
private:
	char *m_String;                    //私有成员 保存字符串
};

mString::~mString(){                   //析构函数
	cout<<"Destructing"<<endl;
	if(m_String!=NULL){
		delete []m_String;
		m_String=NULL;
	}
}

mString::mString(const char *str){    //构造函数
	cout<<"Constructing"<<endl;
	if(str==NULL){
		m_String=new char[1];        //分配一个字节
		*m_String='\0';              //字符串结束符
	}else{
		m_String=new char[strlen(str)+1];   
		strcpy(m_String,str);
	}
}

mString::mString(const mString &other){  //复制构造函数
	cout<<"Constructing Copy"<<endl;
	m_String=new char[strlen(other.m_String)+1];
	strcpy(m_String,other.m_String);
}


mString& mString::operator=(const mString &other){    //运算符重载
	cout<<"Operate=Function"<<endl;
	if(this==&other)         //检测是否为同一对象
		return *this;
	delete []m_String;       //释放堆内存
	m_String=new char[strlen(other.m_String)+1];
	strcpy(m_String,other.m_String);
	return *this;
}

void zhixing(){
	mString a("hello");
	mString b("world");
	mString c(a);
	c=b;
}

int main(){
	zhixing();
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/160.png)

#### 27.分析代码写结果--C++类个成员函数关系

```cpp
#include <iostream>

using namespace std;

class A{
private:
	int num;
public:
	A(){
		cout<<"Default constructor"<<endl;
	}
	~A(){
		cout<<"Desconstructor "<<num<<endl;
	}
	A(const A &a){
		cout<<"Copy constructor"<<endl;
	}
	void operator=(const A &a){
		cout<<"Overload operator"<<endl;
	}
	void SetNum(int n){
		num=n;
	}
};

void zhixing(){
	A a1;            //调用普通构造函数
	A a2(a1);        //调用复制构造函数
	A a3=a1;         //调用复制构造函数
	A &a4=a1;        //引用a1
	a1.SetNum(1);
	a2.SetNum(2);
	a3.SetNum(3);
	a4.SetNum(4);
}

int main(){
	zhixing();
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/161.png)

#### 28.分析代码写输出--C++类的临时对象

```cpp
#include <iostream>

using namespace std;

class B{
public:
	B(){
		cout<<"default constructor"<<endl;
	}
	~B(){
		cout<<"destructed"<<endl;
	}
	B(int i):data(i){
		cout<<"constructed by parameter "<<data<<endl;
	}
private:
	int data;
};

B Play(B b){
	return b;
}

void zhixing1(){
	B t1=Play(5);         //调用普通构造函数
	B t2=Play(t1);        //调用复制构造函数
}

void zhixing2(){
	B t1=Play(5);  
	B t2=Play(10);  
}

int main(){
	zhixing1();
	system("pause");
}
```

程序解析：

这里调用Play()函数时，有两种参数类型的传递方式：

（1）如果传递的参数时整型数，那么在其函数栈中首先会调用带参数的构造函数产生一个临时对象b，然后返回前（在return代码执行时）调用类的复制构造函数生成临时对象（这样函数返回后主函数的对象就被初始化了），最后这个临时对象b会在函数返回时（在return代码执行后）析构。

（2）如果传递的参数时B类对象，只有第一步与上面不同，即其函数栈中首先调用复制构造函数产生一个临时对象b，其余步骤完全相同。

zhixing1()函数执行结果：

![](/images/posts/C++/162.png)

zhixing2()函数执行结果：

![](/images/posts/C++/163.png)

为了更加详细说明结果，在B类中加入一个自定义的复制构造函数：

```cpp
B(B &b){
	cout<<"copy constructor"<<endl;
	data=b.data;
}
```

zhixing1()函数执行结果：

![](/images/posts/C++/164.png)

zhixing2()函数执行结果：

![](/images/posts/C++/165.png)