---
layout: post
title: C++智能指针
categories: C和C++基础
description: C++智能指针
keywords: 智能指针
---

#### 什么是智能指针

```cpp
void func(string & str)
{
    string * ps = new string(str);
	.
	.
	.
    delete ps;
}
```

在编程的过程中，我们时常使用new申请堆内存空间，如上程序中ps指针指向这个空间。堆内存空间和栈空间不一样，需要程序员delete释放，但是我们经常忘记这么做。

于是我们设想：**func函数终止时候，本地变量都将自动从栈内存中删除，因此指针ps占据的内存将被释放，如果ps指向的内存也被自动释放，那该有多好啊**。我们知道析构函数有这个功能，如果ps有一个析构函数，该析构函数将在ps过期时自动释放它指向的内存。但ps的问题在于，它只是一个常规指针，不是有析构凼数的类对象指针。如果它指向的是对象，则可以在对象过期时，让它的析构函数删除指向的内存。

以上就是auto_ptr、unique_ptr和shared_ptr这几个智能指针背后的设计思想。**将基本类型指针封装为类对象指针（这个类肯定是个模板，以适应不同基本类型的需求），并在析构函数里编写delete语句删除指针指向的内存空间**。

如果要使用智能指针，**需要包含头义件memory（智能指针所在的头文件），并将指向string的指针替换为指向string的智能指针对象**。

```cpp
#include <memory>
#include <iostream>
#include <memory>
#include <string>

using namespace std;

void func(string &str)
{
    auto_ptr<string> ps(new string(str));
	cout<<*ps<<endl;
	//delete ps;       //不再使用delete
}

int main(){
	string str="Hello";
	func(str);
	system("pause");
}
```

#### C++智能指针介绍

STL一共给我们提供了四种智能指针：**auto_ptr、unique_ptr、shared_ptr和weak_ptr**。模板auto_ptr是C++98提供的解决方案，C+11已将将其摒弃，并提供了另外两种解决方案。然而，虽然auto_ptr被摒弃，但它已使用了好多年：同时，如果您的编译器不支持其他两种解决力案，auto_ptr将是唯一的选择。

所有的智能指针类都有一个explicit显式构造函数，以指针作为参数。比如auto_ptr的类模板原型为：

```cpp
templet<class T>
class auto_ptr {
	explicit auto_ptr(X* p = 0) ; 
	.
	.
	.
};
```

因此不能自动将指针转换为智能指针对象，必须显式调用：

```cpp
shared_ptr<double> pd; 

double *p_reg = new double;
pd = p_reg;                               // 不允许 (隐式类型转换)

pd = shared_ptr<double>(p_reg);           // 允许 (显式类型转换)

shared_ptr<double> pshared = p_reg;       // 不允许 (隐式类型转换)

shared_ptr<double> pshared(p_reg);        // 允许 (显式类型转换)
```

```cpp
#include <iostream>
#include <string>
#include <memory>

using namespace std;

auto_ptr<string> demo(const char * s)
{
    auto_ptr<string> temp (new string (s)); 
    return temp;
}

int main(){
	auto_ptr<string> sptr;
	sptr=(new string("ad"));                  //报错
	sptr(new string("ad"));                   //报错
	sptr=auto_ptr<string>(new string("ad"));  //通过

	auto_ptr<string> ps;
	ps = demo("Uniquely special");
	cout<<*ps<<endl;
	system("pause");
}
```

使用例子：

```cpp
#include <iostream>
#include <string>
#include <memory>

using namespace std;

class report{
private:
    string str;                         //字符串str

public:
	report(const string s):str(s){      //构造函数
		cout<<"Object created.\n";
	}

	~report(){                          //析构函数
		cout<<"Object deleted.\n";
	}

	void comment() const{               //打印字符串
		cout<<str<<endl;
	}
};

int main() {

	{
		auto_ptr<report> ps(new report("using auto ptr"));
		ps->comment();
	}

	{
		shared_ptr<report> ps(new report("using shared ptr"));
		ps->comment();
	}

	{
		unique_ptr<report> ps(new report("using unique ptr"));
		ps->comment();
	}

	system("pause");
}
```

![](/images/posts/C++/190.png)

#### 为什么摒弃auto_ptr？

```cpp
auto_ptr<string> ps(new string ("Hello”));
auto_ptr<string> vocation; 
vocaticn = ps;
```

如果ps和vocation是常规指针，则两个指针将指向同一个string对象。但是**如果是智能指针，这是不能接受的，因为程序将试图删除同一个对象两次，一次是ps过期的时候，另一次是vocation过期的时候**。要避免这种问题，方法有多种：

1.**定义陚值运算符，使之执行深复制**。这样两个指针将指向不同的对象，其中的一个对象是另一个对象的副本，缺点是浪费空间，所以智能指针都未采用此方案。

2.**建立所有权（ownership）概念**。对于特定的对象，只能有一个智能指针可拥有，这样只有拥有对象的智能指针的构造函数会删除该对象。然后让赋值操作转让所有权。这就是用于auto_ptr和uniqiie_ptr 的策略，但unique_ptr的策略更严格。

3.**创建智能更高的指针，跟踪引用特定对象的智能指针数**。这称为引用计数。例如，赋值时，计数将加1，而指针过期时，计数将减1,。当减为0时才调用delete。这是shared_ptr采用的策略。


```cpp
#include <iostream>
#include <string>
#include <memory>

using namespace std;

int main() {

	auto_ptr<string> films[5] =
	{
		auto_ptr<string> (new string("Fowl Balls")),
		auto_ptr<string> (new string("Duck Walks")),
		auto_ptr<string> (new string("Chicken Runs")),
		auto_ptr<string> (new string("Turkey Errors")),
		auto_ptr<string> (new string("Goose Eggs"))
	};

	auto_ptr<string> pwin;
	//films[2] loses ownership 
	//将所有权从films[2]转让给pwin 
	//此时films[2]不再引用该字符串从而变成空指针
	pwin = films[2]; 

	cout << "The nominees for best avian baseballl film are\n";
	for(int i = 0; i < 5; ++i)
		cout << *films[i] << endl;    //程序会崩溃 因为films[2]智能指针已经失去所有权

	cout << "The winner is " << *pwin << endl;
	system("pause");
}
```

films[2]已经是空指针了，下面输出访问空指针当然会崩溃了。但这里如果把auto_ptr换成shared_ptr或unique_ptr后，程序就不会崩溃，原因如下：

1.使用shared_ptr时运行正常，因为shared_ptr采用引用计数，pwin和films[2]都指向同一块内存，在释放空间时因为事先要判断引用计数值的大小因此不会出现多次删除一个对象的错误。

2.使用unique_ptr时编译出错，与auto_ptr一样，unique_ptr也采用所有权模型，但在使用unique_ptr时，程序不会等到运行阶段崩溃，而在编译器因下述代码行出现错误：

```cpp
unique_ptr<string> pwin;
pwin = films[2];          // films[2] loses ownership.
```

指导你发现潜在的内存错误。这就是为何要摒弃auto_ptr的原因，一句话总结就是：避免潜在的内存崩溃问题。


#### unique_ptr为何优于auto_ptr？

```cpp
auto_ptr<string> p1(new string ("auto"));      //#1
auto_ptr<string> p2;                           //#2
p2 = p1;                                       //#3
```

在语句#3中，p2接管string对象的所有权后，p1的所有权将被剥夺，可防止p1和p2的析构函数试图刪同—个对象。但如果程序随后试图使用p1，这将是件坏事，因为p1不再指向有效的数据。以上程序能编译通过。

```cpp
unique_ptr<string> p3(new string ("auto"));    //#4
unique_ptr<string> p4;                         //#5
p4 = p3;                                       //#6
```

该程序编译报错，因为编译器认为语句#6非法，避免了p3不再指向有效数据的问题。因此，unique_ptr比auto_ptr更安全。

同时unique_ptr还有更聪明的地方。有时候，会将一个智能指针赋给另一个并不会留下危险的悬挂指针。假设有如下函数定义：

```cpp
#include <iostream>
#include <string>
#include <memory>

using namespace std;

unique_ptr<string> demo(const char * s)
{
    unique_ptr<string> temp (new string (s)); 
    return temp;
}

int main() {
	unique_ptr<string> ps;
	ps = demo("Uniquely special");
	cout<<*ps<<endl;
	system("pause");
}
```

demo()返回一个临时unique_ptr，然后ps接管了原本归返回的unique_ptr所有的对象，而返回时临时的unique_ptr被销毁，也就是说没有机会使用unique_ptr来访问无效的数据。换句话来说，这种赋值是不会出现任何问题的，即没有理由禁止这种赋值。实际上，编译器确实允许这种赋值，这正是unique_ptr更聪明的地方。**当程序试图将一个unique_ptr赋值给另一个时，如果源unique_ptr是个临时右值，编译器允许这么做；如果源unique_ptr将存在一段时间，编译器将禁止这么做**，比如：

```cpp
unique_ptr<string> pu1(new string ("hello world"));
unique_ptr<string> pu2;
pu2 = pu1;                                        //#1 not allowed
unique_ptr<string> pu3;
pu3 = unique_ptr<string>(new string ("You"));     //#2 allowed
```

#### 如何选择智能指针？

（1）如果程序要使用多个指向同一个对象的指针，应选择shared_ptr。

（2）如果程序不需要多个指向同一个对象的指针，则可使用unique_ptr。如果函数使用new分配内存，并返还指向该内存的指针，将其返回类型声明为unique_ptr是不错的选择。这样，所有权转让给接受返回值的unique_ptr，而该智能指针将负责调用delete。可将unique_ptr存储到STL容器中，只要不调用将一个unique_ptr复制或赋给另一个算法（如sort()）。例如，可在程序中使用类似于下面的代码段。

```cpp
#include <iostream>
#include <string>
#include <memory>
#include <vector>

using namespace std;

unique_ptr<int> make_int(int n)                 //申请空间 产生智能指针
{
    return unique_ptr<int>(new int(n));
}

void show(unique_ptr<int> &p1)                  //unique_ptr必须要引用 因为不能赋值
{
    cout <<*p1<<" ";
}

int main() {
	int size=3;
	vector<unique_ptr<int> > vp(size);

	 for(int i = 0; i < vp.size(); i++)
        vp[i] = make_int(rand()%1000);          //随机产生1000以内的整数  copy temporary unique_ptr

	 vp.push_back(make_int(rand() % 1000));     //ok because arg is temporary

	 for(int i = 0; i < vp.size(); i++)         //显示
		 show(vp[i]);

	system("pause");
}
```

其中push_back调用没有问题，因为它返回一个临时unique_ptr，该unique_ptr被赋给vp中的一个unique_ptr。另外，如果按值而不是按引用给show()传递对象将非法，因为这将导致使用一个来自vp的非临时unique_ptr初始化p1，而这是不允许的。前面说过，编译器将发现错误使用unique_ptr的企图。

在unique_ptr为右值时，可将其赋给shared_ptr，这与将一个unique_ptr赋给一个需要满足的条件相同：

```cpp
unique_ptr<int> pup(make_int(rand()%1000));     //ok
shared_ptr<int> spp(pup);                       //not allowed, pup as lvalue
shared_ptr<int> spr(make_int(rand()%1000));     //ok
```

模板shared_ptr包含一个显式构造函数，可用于将右值unique_ptr转换为shared_ptr。shared_ptr将接管原来归unique_ptr所有的对象。在满足unique_ptr要求的条件时，也可使用auto_ptr，但unique_ptr是更好的选择。