---
layout: post
title: C++ 虚函数内存布局
categories: C和C++基础
description: C++ 虚函数内存布局
keywords: C++ 虚函数内存布局
---

部分内容参考陈皓先生的一篇文章《C++虚函数表解析》(详情见http://blog.csdn.net/haoel)。

C++中的虚函数的作用主要是实现了多态的机制。所谓的多态就是用父类的指针指向其子类的实例，然后通过父类的指针调用子类的成员函数。这种技术可以让父类的指针有“多种形态”，这是一种泛型编程（所谓泛型技术，说白了就是试图使用不变的代码来实现可变的算法。比如：模板技术、函数重载、虚函数技术，要么是试图做到在编译时决议，要么试图做到运行时决议。）

#### 虚函数表

虚函数（Virtual Function）是通过一张虚函数表（Virtual Table）来实现的，简称为V-Table。在这个表中，主要是一个类的虚函数的地址表，这张表解决了继承、覆盖的问题，保证其真实反应实际的函数。在有虚函数的类的实例中这个表被分配在该实例的内存中，所以当我们用父类的指针来操作一个子类的时候，这张虚函数表就显得由为重要了，它就像一个地图一样，指明了实际所应该调用的函数。

C++的编译器应该是保证虚函数表的指针存在于对象实例内存中最前面的位置（这是为了保证取到虚函数表有最高的性能，如果有多层继承或是多重继承的情况下）。这意味着我们通过对象实例的地址得到这张虚函数表，然后就可以遍历其中函数指针，并调用相应的函数。

```cpp
#include <iostream>
using namespace std;

class Myclass{
public:
	int x;
	char c;

	Myclass(){};               //构造函数
	virtual ~Myclass(){};      //析构函数
 
	virtual void func(void){
		cout<<"virtual func"<<endl;
	};
};
 
int main(){
	Myclass obj;
	printf("对象的起始地址为：%x\n",&obj);
	printf("变量x的地址为：   %x\n",&obj.x);
	printf("变量c的地址为：   %x\n",&obj.c );
	printf("对象obj的大小为:  %d\n",sizeof(obj));

	typedef void(*Fun)(void);  //定义函数指针类型
	Fun pFun = NULL;           //定义函数指针
	cout<<"虚函数表地址："<<(int*)(&obj)<< endl;
    cout<<"虚函数表 — 第一个函数地址："<<(int*)*(int*)(&obj)<<endl;
	pFun =(Fun)*((int*)*(int*)(&obj)+1);
	pFun();
	system("pause");
}
```

![](/images/posts/C++/218.png)


**虚函数表的指针存在于对象实例内存中最前面的位置，所有的虚函数共享同一张虚函数表**。

c++中一个类中无非有四种成员：静态数据成员和非静态数据成员，静态函数和非静态函数。

1.非静态数据成员被放在每一个对象体内作为对象专有的数据成员。

2.静态数据成员被提取出来放在程序的静态数据区内，为该类所有对象共享，因此只存在一份。


3.静态和非静态成员函数最终都被提取出来放在程序的代码段中并为该类所有对象共享，因此每一个成员函数也只能存在一份代码实体。在c++中类的成员函数都是保存在静态存储区中的，那静态函数也是保存在静态存储区中的，他们都是在类中保存同一个惫份。

因此，构成对象本身的只有数据，任何成员函数都不隶属于任何一个对象，非静态成员函数与对象的关系就是绑定，绑定的中介就是this指针。成员函数为该类所有对象共享，不仅是处于简化语言实现、节省存储的目的，而且是为了使同类对象有一致的行为。同类对象的行为虽然一致，但是操作不同的数据成员。


#### 一般继承（无虚函数覆盖）

假设有这么一个类：

```cpp
class Base {
   public:
     virtual void f(){ cout << "Base::f" << endl; }
     virtual void g(){ cout << "Base::g" << endl; }
     virtual void h(){ cout << "Base::h" << endl; }
};
```

则在对象Base b的内存中有

![](/images/posts/C++/220.png)

然后又下面的继承关系：

![](/images/posts/C++/219.png)

请注意，在这个继承关系中，子类没有重载任何父类的函数。那么，在派生类的实例中，其虚函数表如下所示：

对于实例Derive d的虚函数表如下：

![](/images/posts/C++/221.png)

总结：

1）虚函数按照其声明顺序放于表中。

2）父类的虚函数在子类的虚函数前面。

#### 一般继承（有虚函数覆盖）

覆盖父类的虚函数是很显然的事情，不然，虚函数就变得毫无意义。

![](/images/posts/C++/222.png)

只覆盖了父类的一个函数：f()。对于派生类的实例，则其虚函数表：

![](/images/posts/C++/223.png)

我们从表中可以看到下面几点：

1）覆盖的f()函数被放到了虚表中原来父类虚函数的位置。

2）没有被覆盖的函数依旧。

对于下面这样的程序：

```cpp
Base *b = new Derive();
b->f();
```

由b所指的内存中的虚函数表的f()的位置已经被Derive::f()函数地址所取代，于是在实际调用发生时，是Derive::f()被调用了。从而实现了多态。

```cpp
#include <iostream>
using namespace std;

class Myclass{
public:
	int x;
	char c;

	Myclass(){};               //构造函数
	virtual ~Myclass(){};      //析构函数
 
	virtual void func(void){
		cout<<"virtual func"<<endl;
	};
};


class derivedClass:public Myclass
{
public:
	int Subx;
	derivedClass(){};
	~derivedClass(){};
	virtual void func(void) {
		cout<<"func in derivedClass"<<endl;
	};
};
 
int main(){
	derivedClass obj;
	printf("对象的起始地址为：%x\n",&obj);
	printf("变量x的地址为：   %x\n",&obj.x);
	printf("变量c的地址为：   %x\n",&obj.c);
	printf("变量Subx的地址为：%x\n",&obj.Subx);
	printf("对象obj的大小为:  %d\n",sizeof(obj));
	system("pause");
}
```

![](/images/posts/C++/224.png)

**单继承的对象的内存布局，第一个为虚函数表指针vtbl，其后为成员且先基类后子类，虚函数表里包含了所有的虚函数的地址，以NULL结束。虚函数如果子类有重写，就由子类的重新的代替**。

#### 多重继承（无虚函数覆盖）

在多重继承中的情况中，假设有下面这样一个类的继承关系。注意：子类并没有覆盖父类的函数。

![](/images/posts/C++/225.png)

对于子类实例中的虚函数表，是下面这个样子：

![](/images/posts/C++/226.png)

我们可以看到：

1）每个父类都有自己的虚表。
2）子类的成员函数被放到了第一个父类的表中。（所谓的第一个父类是按照声明顺序来判断的）
 
这样做就是为了解决不同的父类类型的指针指向同一个子类实例，而能够调用到实际的函数。

```cpp
#include <iostream>
using namespace std;

class Base1{
public:
	virtual void f(){cout<<"Base1 f()"<<endl;}
	virtual void g(){cout<<"Base1 g()"<<endl;}
	virtual void h(){cout<<"Base1 h()"<<endl;}
};

class Base2{
public:
	virtual void f(){cout<<"Base2 f()"<<endl;}
	virtual void g(){cout<<"Base2 g()"<<endl;}
	virtual void h(){cout<<"Base2 h()"<<endl;}
};

class Base3{
public:
	virtual void f(){cout<<"Base3 f()"<<endl;}
	virtual void g(){cout<<"Base3 g()"<<endl;}
	virtual void h(){cout<<"Base3 h()"<<endl;}
};

class Derived:public Base1,Base2,Base3{
	virtual void f1(){cout<<"Derived f1()"<<endl;}
	virtual void g1(){cout<<"Derived g1()"<<endl;}
};

int main(){
	Base1 *b1;
	b1=new Derived();
	b1->f();
	system("pause");
}
```

![](/images/posts/C++/227.png)


#### 多重继承（有虚函数覆盖）

如果发生虚函数覆盖的情况，我们在子类中覆盖了父类的f()函数。

![](/images/posts/C++/228.png)

对于子类实例中的虚函数表的图：

![](/images/posts/C++/229.png)

三个父类虚函数表中的f()的位置被替换成了子类的函数指针。这样，我们就可以任一静态类型的父类来指向子类，并调用子类的f()了。

```cpp
#include <iostream>
using namespace std;

class Base1{
public:
	virtual void f(){cout<<"Base1 f()"<<endl;}
	virtual void g(){cout<<"Base1 g()"<<endl;}
	virtual void h(){cout<<"Base1 h()"<<endl;}
};

class Base2{
public:
	virtual void f(){cout<<"Base2 f()"<<endl;}
	virtual void g(){cout<<"Base2 g()"<<endl;}
	virtual void h(){cout<<"Base2 h()"<<endl;}
};

class Base3{
public:
	virtual void f(){cout<<"Base3 f()"<<endl;}
	virtual void g(){cout<<"Base3 g()"<<endl;}
	virtual void h(){cout<<"Base3 h()"<<endl;}
};

class Derived:public Base1,public Base2,public Base3{
	virtual void f(){cout<<"Derived f()"<<endl;}
	virtual void g1(){cout<<"Derived g1()"<<endl;}
};

int main(){
	Derived d;
    Base1 *b1 = &d;
    Base2 *b2 = &d;
    Base3 *b3 = &d;
    b1->f();             //Derive::f()
    b2->f();             //Derive::f()
    b3->f();             //Derive::f()
 			             
    b1->g();             //Base1::g()
    b2->g();             //Base2::g()
    b3->g();             //Base3::g()
	system("pause");
}
```

![](/images/posts/C++/230.png)

**与单继承相同的是所有的虚函数都包含在虚函数表中，所不同的多重继承有多个虚函数表，当子类对父类的虚函数有重写时，子类的函数覆盖父类的函数在对应的虚函数位置，当子类有新的虚函数时，这些虚函数被加在第一个虚函数表的后面**。

#### 通过父类型的指针访问子类自己的未覆盖虚函数

我们知道，子类没有重载父类的虚函数是一件毫无意义的事情。因为多态也是要基于函数重载的。虽然在上面的图中我们可以看到Base1的虚表中有Derive的虚函数，但我们根本不可能使用下面的语句来调用子类的自有虚函数：
 
```cpp
Base1 *b1 = new Derive();
b1->g1();                   //编译出错
```
 
任何妄图使用父类指针想调用子类中的未覆盖父类的成员函数的行为都会被编译器视为非法，所以这样的程序根本无法编译通过。

#### 访问non-public的虚函数

如果父类的虚函数是private或是protected的，但这些非public的虚函数同样会存在于虚函数表中，所以，我们同样可以使用访问虚函数表的方式来访问这些non-public的虚函数，这是很容易做到的。

```cpp
#include <iostream>
using namespace std;

class Base{
	private:
    virtual void f(){cout<<"Base::f"<<endl;}
};
 
class Derive : public Base{
 
};
 
typedef void(*Fun)(void);
 
void main(){
    Derive d;
    Fun pFun = (Fun)*((int*)*(int*)(&d)+0);
    pFun();
	system("pause");
}
```

#### 一个关于多重继承的虚函数表访问的例程

```cpp
#include <iostream>
using namespace std;
 
class Base1{
public:
	virtual void f(){ cout<<"Base1::f"<<endl;}
    virtual void g(){ cout<<"Base1::g"<<endl;}
    virtual void h(){ cout<<"Base1::h"<<endl;}
};
 
class Base2 {
public:
	virtual void f(){cout<<"Base2::f"<<endl;}
	virtual void g(){cout<<"Base2::g"<<endl;}
	virtual void h(){cout<<"Base2::h"<<endl;}
};
 
class Base3 {
public:
	virtual void f(){cout<<"Base3::f"<<endl;}
	virtual void g(){cout<<"Base3::g"<<endl;}
	virtual void h(){cout<<"Base3::h"<<endl;}
};
 
class Derive : public Base1,public Base2,public Base3{
public:
	virtual void f(){cout<<"Derive::f"<<endl;}
	virtual void g1(){cout<<"Derive::g1"<<endl;}
};
 
typedef void(*Fun)(void);
 
int main(){
	Fun pFun = NULL;
	
	Derive d;
	int** pVtab = (int**)&d;
	
	//Base1's vtable
	//pFun = (Fun)*((int*)*(int*)((int*)&d+0)+0);
	pFun = (Fun)pVtab[0][0];
	pFun();
	
	//pFun = (Fun)*((int*)*(int*)((int*)&d+0)+1);
	pFun = (Fun)pVtab[0][1];
	pFun();
	
	//pFun = (Fun)*((int*)*(int*)((int*)&d+0)+2);
	pFun = (Fun)pVtab[0][2];
	pFun();
	
	//Derive's vtable
	//pFun = (Fun)*((int*)*(int*)((int*)&d+0)+3);
	pFun = (Fun)pVtab[0][3];
	pFun();
	
	//The tail of the vtable
	pFun = (Fun)pVtab[0][4];
	cout<<pFun<<endl;

////////////////////////////////////////////////////////
	
	//Base2's vtable
	//pFun = (Fun)*((int*)*(int*)((int*)&d+1)+0);
	pFun = (Fun)pVtab[1][0];
	pFun();
	
	//pFun = (Fun)*((int*)*(int*)((int*)&d+1)+1);
	pFun = (Fun)pVtab[1][1];
	pFun();
	
	pFun = (Fun)pVtab[1][2];
	pFun();
	
	//The tail of the vtable
	pFun = (Fun)pVtab[1][3];
	cout<<pFun<<endl;

////////////////////////////////////////////////////////

	//Base3's vtable
	//pFun = (Fun)*((int*)*(int*)((int*)&d+1)+0);
	pFun = (Fun)pVtab[2][0];
	pFun();
	
	//pFun = (Fun)*((int*)*(int*)((int*)&d+1)+1);
	pFun = (Fun)pVtab[2][1];
	pFun();
	
	pFun = (Fun)pVtab[2][2];
	pFun();
	
	//The tail of the vtable
	pFun = (Fun)pVtab[2][3];
	cout<<pFun<<endl;
	
	system("pause");
}
```

![](/images/posts/C++/231.png)


#### 虚继承

虚继承，使公共的基类在子类中只有一份，虚继承在多重继承的基础上多了vbtable来存储到公共基类的偏移。

![](/images/posts/C++/232.png)

D2覆盖了B的vB，GD覆盖了D1的vD1同时也覆盖B的vB，GD也有自己的虚函数vGD。

![](/images/posts/C++/233.png)

**总结：虚继承，使公共的基类在子类中只有一份，我们看到虚继承在多重继承的基础上多了vbtable来存储到公共基类的偏移**。


