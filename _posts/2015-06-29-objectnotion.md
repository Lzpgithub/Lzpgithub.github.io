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