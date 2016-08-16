---
layout: post
title: c++中四种类型转换机制
categories: C和C++基础
description: c++中四种类型转换机制
keywords: c++中四种类型转换机制
---

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

**通过这两种方式，C语言中大部分的类型转换都可以顺利进行编译通过。至于能不能进行转换，转换后的结果如何，编译器不管，需要用户自己去控制，有可能使程序变得特别危险**。

比如：

```cpp
int main(){
    int i = 100;
    void * p =(void *)&i;
	static_cast<int>(p);     //报错
    int j = (int)(p);        //编译通过
    return 0;
}
```

妄图将void*转换成int型，static_cast是不干的，要做这种无法无天的事情，你只能使用C的强制转换。因此并不能完全说static_cast是强制转换的替代品。

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

##### 总结

类似于C风格的强制转换。无条件转换，静态类型转换。用于：

（1.）基类和子类之间转换：其中子类指针转换成父类指针是安全的;但父类指针转换成子类指针是不安全的。(**基类和子类之间的动态类型转换建议用dynamic_cast**)

（2.）基本数据类型转换。enum, struct, int, char, float等。static_cast不能进行无关类型(如非基类和子类)指针之间的转换。

（3.）把空指针转换成目标类型的空指针，把任何类型的表达式转换成void类型。

（4.）static_cast不能去掉类型的const、volitale属性(用const_cast)。

```cpp
int n = 6;
double d = static_castdouble>(n);      //基本类型转换
int *pn = &n;
double *d = static_castdouble *>(&n)   //无关类型指针转换，编译错误
void *p = static_castvoid *>(pn);      //任意类型转换成void类型
```

#### dynamic_cast

有条件转换，动态类型转换，运行时类型安全检查(转换失败返回NULL)：

1.安全的基类和子类之间转换（向上转换本来就安全，dynamic_cast把向下转换返回NULL，因此也是安全的）。

2.必须要有虚函数（转换信息都在虚函数表里面）。

3.相同基类不同子类之间的交叉转换。但结果是NULL，因此也是安全的。

```cpp
class BaseClass{
public:
　　int m_iNum;
	//基类必须有虚函数。保持多态特性才能使用dynamic_cast
　　virtual void foo(){};
};

class DerivedClass: public BaseClass{
public:
　　char *m_szName[100];
　　void bar(){};
};

BaseClass* pb = new DerivedClass();

//子类->父类，静态类型转换，正确但不推荐
DerivedClass *pd1 = static_cast<DerivedClass*>(pb);

//子类->父类，动态类型转换，正确
DerivedClass *pd2 = dynamic_cast<DerivedClass*>(pb);

BaseClass* pb2 = new BaseClass();

//父类->子类，静态类型转换，危险！访问子类m_szName成员越界
DerivedClass *pd21 = static_cast<DerivedClass*>(pb2);

//父类->子类，动态类型转换，安全的。pd22结果是NULL
DerivedClass *pd22 = dynamic_cast<DerivedClass*>(pb2);
```

**static_cast字面意思是静态转换，编译期间就能判断是否可以转换成功，但是无法识别兄弟指针之间的转换，而dynamic_cast是运行时转换，可以编译通过，但是可以与NULL指针的比较来判断是否转换成功！**

```cpp
#include <iostream>
using namespace std;
class A
{
public:
   virtual ~A(){}         //虚函数 多态
};
class B:public A
{
public:
    int m;
};

int main(){
	A* pObjA = new A();
	B* pObjB1  = NULL;
	pObjB1 = dynamic_cast<B*>(pObjA);  //编译通过 
	pObjB1->m=10;                      //运行出错 因为pObjB1是空的

	B* pObjB2 = NULL;
	pObjB2 = static_cast<B*>(pObjA);   //编译通过 
	pObjB2->m=10;                      //运行通过 但是危险 访问子类m成员越界
	system("pause");
}
```

#### const_cast

去掉类型的const或volatile属性。

**const_cast中类型必须是指针、引用或指向对象成员的指针**。

```cpp
struct SA {
	int i;
};

const SA ra;
ra.i = 10;                     //直接修改const类型，编译错误
SA &rb = const_castSA&>(ra);
rb.i = 10;
```

```cpp
#include <iostream>
using namespace std;

int main(){
	const int i=1;
	int &j=const_cast<int&>(i);
	j=3;
	cout<<i<<" "<<j<<endl;  //输出1和3
	system("pause");
}
```

#### reinterpreter_cast

仅仅重新解释类型，但没有进行二进制的转换：

1.转换的类型必须是一个指针、引用、算术类型、函数指针或者成员指针。
 
2.在比特位级别上进行转换。它可以把一个指针转换成一个整数，也可以把一个整数转换成一个指针(先把一个指针转换成一个整数，在把该整数转换成原类型的指针，还可以得到原先的指针值)。但不能将非32bit的实例转成指针。

3.最普通的用途就是在函数指针类型之间进行转换。

4.很难保证移植性。

reinterpret_cast运算符是用来处理无关类型之间的转换；它会产生一个新的值，这个值会有与原始参数（expressoin）有完全相同的比特位。

从指针类型到一个足够大的整数类型

从整数类型或者枚举类型到指针类型

从一个指向函数的指针到另一个不同类型的指向函数的指针

从一个指向对象的指针到另一个不同类型的指向对象的指针

从一个指向类函数成员的指针到另一个指向不同类型的函数成员的指针

从一个指向类数据成员的指针到另一个指向不同类型的数据成员的指针


```cpp
int doSomething(){return 0;};

//FuncPtr是一个指向函数的指针，该函数没有参数，返回值类型为 void
typedef void(*FuncPtr)();

//10个FuncPtrs指针的数组 让我们假设你希望把一个指向下面函数的指针存入funcPtrArray数组：
FuncPtr funcPtrArray[10];

//编译错误！类型不匹配，reinterpret_cast可以让编译器以你的方法去看待它们：funcPtrArray
funcPtrArray[0] = &doSomething;

//不同函数指针类型之间进行转换
funcPtrArray[0] = reinterpret_castFuncPtr>(&doSomething);
```

#### 总结

去const属性用const_cast；

基本类型转换用static_cast；

多态类之间的类型转换用daynamic_cast；

不同类型的指针类型转换用reinterpreter_cast；


部分内容引用自：http://www.cnblogs.com/bastard/archive/2011/12/14/2288117.html

