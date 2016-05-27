---
layout: post
title: 拷贝构造函数
categories: C和C++基础
description: 拷贝构造函数
keywords: 拷贝构造函数
---

#### 对象的赋值

如果对一个类定义了两个或多个对象，则这些同类的对象之间可以互相赋值，或者说，一个对象的值可以赋给另一个同类的对象。这里所指的对象的值是指对象中所有数据成员的值。

对象之间的赋值也是通过赋值运算符“=”进行的。本来，赋值运算符“=”只能用来对单个的变量赋值，现在被扩展为两个同类对象之间的赋值，这是通过对赋值运算符的重载实现的。实际这个过程是通过成员复制来完成的，即将一个对象的成员值一一复制给另一对象的对应成员。对象赋值的一般形式为：

```cpp
对象名1 = 对象名2;
```

注意对象名1和对象名2必须属于同一个类。

```cpp
#include <iostream>
using namespace std;
class Box
{
public:
	Box(int=10,int=10,int=10); //声明有默认参数的构造函数
	int volume();
private:
	int height;
	int width;
	int length;
};

Box::Box(int h,int w,int len)
{
	height=h;
	width=w;
	length=len;
}

int Box::volume( )
{
	return(height*width*length); //返回体积
}

int main( )
{
	Box box1(15,30,25),box2;    //定义两个对象box1和box2
	cout<<"The volume of box1 is"<<box1.volume( )<<endl;
	box2=box1;                  //将box1的值赋给box2
	cout<<"The volume of box2 is"<<box2.volume( )<<endl;
	return 0;
}
```

注意：

(1)对象的赋值只对其中的数据成员赋值，而不对成员函数赋值。

(2)类的数据成员中不能包括动态分配的数据，否则在赋值时可能出现严重后果。


#### 对象的复制

有时需要用到多个完全相同的对象。此外，有时需要将对象在某一瞬时的状态保留下来。这就是对象的复制机制。用一个已有的对象快速地复制出多个完全相同的对象。如：

```cpp
Box box2(box1);
```

其作用是用已有的对象box1去克隆出一个新对象box2。

其一般形式为：

```cpp
类名 对象2(对象1);
```

用对象1复制出对象2。

可以看到：它与前面介绍过的定义对象方式类似，但是括号中给出的参数不是一般的变量，而是对象。在建立对象时调用一个特殊的构造函数——拷贝(复制)构造函数(copy constructor)。这个函数的形式是这样的：

```cpp
//The copy constructor definition.
Box::Box(const Box& b)
{
	height=b.height;
	width=b.width;
	length=b.length;
}
```

复制构造函数也是构造函数，但它只有一个参数，这个参数是本类的对象(不能是其他类的对象)，而且**采用对象的引用的形式(一般约定加const声明，使参数值不能改变，以免在调用此函数时因不慎而使对象值被修改)**。此复制构造函数的作用就是将实参对象的各成员值一一赋给新的对象中对应的成员。

回顾复制对象的语句：

```cpp
Box box2(box1);
```

这实际上也是建立对象的语句，建立一个新对象box2。由于在括号内给定的实参是对象，因此编译系统就调用复制构造函数(它的形参也是对象)，而不会去调用其他构造函数。实参box1的地址传递给形参b(b是box1的引用)，因此执行复制构造函数的函数体时，将box1对象中各数据成员的值赋给box2中各数据成员。如果用户自己未定义复制构造函数，则编译系统会自动提供一个默认的复制构造函数，其作用只是简单地复制类中每个数据成员。

C++还提供另一种方便用户的复制形式，用赋值号代替括号，如：

```cpp
Box box2=box1; //用box1初始化box2
```

其一般形式为：

```cpp
类名 对象名1 =对象名2;
```

可以在一个语句中进行多个对象的复制。如：

```cpp
Box box2=box1,box3=box2;
```

按box1来复制box2和box3。可以看出这种形式与变量初始化语句类似，看起来很直观，用起来很方便，但是其作用都是调用复制构造函数。

请注意对象的复制和对象的赋值在概念上和语法上的不同。对象的赋值是对一个已经存在的对象赋值，因此必须先定义被赋值的对象，才能进行赋值。而对象的复制则是从无到有地建立一个新对象，并使它与一个已有的对象完全相同(包括对象的结构和成员的值)。

```cpp
int main( )
{
	Box box1(15,30,25);              //定义box1
	cout<<"The volume of box1 is"<<box1.volume( )<<endl;
	Box box2=box1,box3=box2;         //按box1来复制box2,box3
	cout<<"The volume of box2 is"<<box2.volume( )<<endl;
	cout<<"The volume of box3 is"<<box3.volume( )<<endl;
}
```

执行完第3行后，3个对象的状态完全相同。

#### 普通构造函数和复制构造函数的区别

(1).在形式上：

```cpp
类名(形参表列);      //普通构造函数的声明，如Box(int h,int w,int len);
类名(类名&对象名);   //复制构造函数的声明，如Box(Box &b);
```

(2).在建立对象时，实参类型不同。系统会根据实参的类型决定调用普通构造函数或复制构造函数。如：

```cpp
Box box1(12,15,16);  //实参为整数，调用普通构造函数
Box box2(box1);      //实参是对象名，调用复制构造函数
```

(3).在什么情况下被调用

普通构造函数在程序中建立对象时被调用。复制构造函数在用已有对象复制一个新对象时被调用，在以下3种情况下需要克隆对象：

a.程序中需要新建立一个对象，并用另一个同类的对象对它初始化，如前面介绍的那样。

b.当函数的参数为类的对象时。在调用函数时需要将实参对象完整地传递给形参，也就是需要建立一个实参的拷贝，这就是按实参复制一个形参，系统是通过调用复制构造函数来实现的，这样能保证形参具有和实参完全相同的值。如：

```cpp
void fun(Box b)      //形参是类的对象
{ }

int main( )
{
	Box box1(12,15,18);
	fun(box1);       //实参是类的对象，调用函数时将复制一个新对象b
	return 0;
}
```

c.函数的返回值是类的对象。在函数调用完毕将返回值带回函数调用处时。此时需要将函数中的对象复制一个临时对象并传给该函数的调用处。如：

```cpp
Box f()           //函数f的类型为Box类类型
{
	Box box1(12,15,18);
	return box1;  //返回值是Box类的对象
}

int main( )
{
	Box box2; //定义Box类的对象box2
	box2=f(); //调用f函数，返回Box类的临时对象，并将它赋值给box2
}
```

以上几种调用复制构造函数都是由编译系统自动实现的，不必由用户自己去调用，读者只要知道在这些情况下需要调用复制构造函数就可以了。

#### 例子总结拷贝构造函数

##### 什么是拷贝构造函数

```cpp
#include <iostream>  
using namespace std;  
  
class CExample {  
private:  
    int a;  
public:  
    //构造函数  
    CExample(int b)  
    { a = b;}  
  
    //一般函数  
    void Show ()  
    {  
       cout<<a<<endl;  
    }  
};  
  
int main()  
{  
    CExample A(100);  
    CExample B = A;    //注意这里的对象初始化要调用拷贝构造函数，而非赋值  
    B.Show ();  
    return 0;  
} 
```

运行程序，屏幕输出100。从以上代码的运行结果可以看出，系统为对象 B 分配了内存并完成了与对象A的复制过程。就类对象而言，相同类型的类对象是通过拷贝构造函数来完成整个复制过程的。


拷贝构造函数的工作过程：

```cpp
#include <iostream>  
using namespace std;  
  
class CExample {  
private:  
    int a;  
public:  
    //构造函数  
    CExample(int b)  
    { a = b;}  
      
    //拷贝构造函数  
    CExample(const CExample& C)  
    {  
        a = C.a;  
    }  
  
    //一般函数  
    void Show ()  
    {  
        cout<<a<<endl;  
    }  
};  
  
int main()  
{  
    CExample A(100);  
    CExample B = A;    //CExample B(A); 也是一样的  
    B.Show ();  
    return 0;  
}   
```

*CExample(const CExample& C)*就是我们自定义的拷贝构造函数。可见，***拷贝构造函数是一种特殊的构造函数，函数的名称必须和类名称一致，它必须的一个参数是本类型的一个引用变量***。


##### 拷贝构造函数的调用时机

1.对象以值传递的方式传入函数参数

```cpp
#include <iostream>

using namespace std;

class CExample   
{  
private:  
	int a;  
  
public:  
	CExample(int b){          //构造函数     
		a = b;  
		cout<<"creat: "<<a<<endl;  
	}  
   
	CExample(const CExample& C){  //拷贝构造   
		a = C.a;  
		cout<<"copy"<<endl;  
	}  
   
	~CExample(){                 //析构函数   
		cout<< "delete: "<<a<<endl;  
	}  
  
	void Show(){  
        cout<<a<<endl;  
    }  
};  
  
void g_Fun(CExample C){         //全局函数，传入的是对象    
	cout<<"test"<<endl;  
}  
  
int main(){  
	CExample test(1);  
	//传入对象  
	g_Fun(test);  
	return 0;  
}  
```

运行结果：

![](/images/posts/C++/22.png)

调用g_Fun()时，会产生以下几个重要步骤：

(1).test对象传入形参时，会先会产生一个临时变量，就叫C吧。

(2).然后调用拷贝构造函数把test的值给C。 这两个步骤有点像：CExample C(test);

(3).等g_Fun()执行完后, 析构掉C对象。


2.对象以值传递的方式从函数返回

```cpp
#include <iostream>

using namespace std;

class CExample   
{  
private:  
	int a;  
  
public:  
	CExample(int b){          //构造函数     
		a = b;  
	}  
   
	CExample(const CExample& C){  //拷贝构造   
		a = C.a;  
		cout<<"copy"<<endl;  
	}  
  
	void Show(){  
        cout<<a<<endl;  
    }  
};  
  
CExample g_Fun(){            //全局函数    
	CExample temp(0);  
	return temp; 
}  
  
int main(){   
	g_Fun();   
	system("pause");
}  
```

运行结果：

![](/images/posts/C++/23.png)

当g_Fun()函数执行到return时，会产生以下几个重要步骤：

(1). 先会产生一个临时变量，就叫XXXX吧。

(2). 然后调用拷贝构造函数把temp的值给XXXX。整个这两个步骤有点像：CExample XXXX(temp);

(3). 在函数执行到最后先析构temp局部变量。

(4). 等g_Fun()执行完后再析构掉XXXX对象。



3.对象需要通过另外一个对象进行初始化

```cpp
CExample A(100);  
CExample B = A;   //等价于 CExample B(A); 
```

##### 浅拷贝和深拷贝

###### 1.默认拷贝构造函数

很多时候在我们都不知道拷贝构造函数的情况下，传递对象给函数参数或者函数返回对象都能很好的进行，**这是因为编译器会给我们自动产生一个拷贝构造函数，这就是“默认拷贝构造函数”**，这个构造函数很简单，仅仅使用“老对象”的数据成员的值对“新对象”的数据成员一一进行赋值，它一般具有以下形式：

```cpp
Rect::Rect(const Rect& r)  
{  
    width = r.width;  
    height = r.height;  
}  
```

当然，以上代码不用我们编写，编译器会为我们自动生成。但是如果认为这样就可以解决对象的复制问题，那就错了，让我们来考虑以下一段代码：

```cpp
#include <iostream>

using namespace std;

class Rect  
{  
public:  
    Rect(){      // 构造函数，计数器加1    
        count++;  
    }  

    ~Rect(){     // 析构函数，计数器减1    
        count--;  
    }  

    static int getCount()       // 返回计数器的值  
    {  
        return count;  
    }  
private:  
    int width;  
    int height;  
    static int count;         // 一静态成员做为计数器  
};  
  
int Rect::count = 0;          // 初始化计数器  
  
int main()  
{  
    Rect rect1;  
    cout<<"The count of Rect: "<<Rect::getCount()<<endl;  
  
    Rect rect2(rect1);       // 使用rect1复制rect2，此时应该有两个对象  
    cout<<"The count of Rect: "<<Rect::getCount()<<endl;  
  
    system("pause"); 
} 
```

运行结果为：

![](/images/posts/C++/24.png)

这段代码对前面的类，加入了一个静态成员，目的是进行计数。在主函数中，首先创建对象rect1，输出此时的对象个数，然后使用rect1复制出对象rect2，再输出此时的对象个数，按照理解，此时应该有两个对象存在，但实际程序运行时，输出的都是1，反应出只有1个对象。此外，在销毁对象时，由于会调用销毁两个对象，类的析构函数会调用两次，此时的计数器将变为负数。说白了，**就是拷贝构造函数没有处理静态数据成员**。

出现这些问题最根本就在于在复制对象时，计数器没有递增，我们重新编写拷贝构造函数即可，如下：

```cpp
Rect(const Rect& r)      // 拷贝构造函数  
{  
    width = r.width;  
    height = r.height;  
    count++;             // 计数器加1  
}  
```

###### 2.浅拷贝

所谓浅拷贝，指的是在对象复制时，**只对对象中的数据成员进行简单的赋值，默认拷贝构造函数执行的也是浅拷贝**。大多情况下“浅拷贝”已经能很好地工作了，**但是一旦对象存在了动态成员，那么浅拷贝就会出问题了**，让我们考虑如下一段代码：

```cpp
#include <iostream>

using namespace std;

class Rect  
{  
public:  
    Rect(){               //构造函数，p指向堆中分配的一空间   
       p = new int(100);  //存储空间赋值100
	   width=1;
	   height=2;
    }  

	~Rect(){              // 析构函数，释放动态分配的空间    
		if(p != NULL){  
			delete p;  
		}  
	}  

private:  
    int width;  
    int height;  
    int *p;              // 一指针成员  
};  
  
int main()  
{  
    Rect rect1;  
    Rect rect2(rect1);   // 使用rect1复制rect2，此时应该有两个对象  
    system("pause"); 
} 
```

在这段代码运行结束之前，会出现一个运行错误。原因就在于在进行对象复制时，对于动态分配的内容没有进行正确的操作。在运行定义rect1对象后，由于在构造函数中有一个动态分配的语句，因此执行后的内存情况大致如下：

![](/images/posts/C++/25.png)

在使用rect1复制rect2时，由于执行的是浅拷贝，只是将成员的值进行赋值，这时**rect1.p= rect2.p**，也即这两个指针指向了堆里的同一个空间，如下图所示：

![](/images/posts/C++/26.png)

![](/images/posts/C++/27.png)

当然，这不是我们所期望的结果，在销毁对象时，两个对象的析构函数将对同一个内存空间释放两次，这就是错误出现的原因。我们需要的不是两个p有相同的值，而是两个p指向不同的空间有相同的值，解决办法就是使用“深拷贝”。

浅拷贝过程中会出现指针成员赋值，也就是两个对象的指针成员都指向同一个空间，当要删除对象时，两个对象都要执行删除指针对应的空间的语句，也就是会导致同一个空间会被释放两次的错误。


###### 2.深拷贝

在“深拷贝”的情况下，对于对象中动态成员，就不能仅仅简单地赋值了，而应该重新动态分配空间，如上面的例子就应该按照如下的方式进行处理：

```cpp
#include <iostream>

using namespace std;

class Rect  
{  
public:  
    Rect(){               //构造函数，p指向堆中分配的一空间   
       p = new int(100);  //存储空间赋值100
	   width=1;
	   height=2;
    }  

	Rect(const Rect& r){  //深拷贝构造函数
		width=r.width;
		height=r.height;
		p=new int;        //为新对象重新动态分配空间  
		*p = *(r.p);
	}

	~Rect(){              //析构函数，释放动态分配的空间    
		if(p != NULL){  
			delete p;  
		}  
	}  

private:  
    int width;  
    int height;  
    int *p;              // 一指针成员  
};  
  
int main()  
{  
    Rect rect1;  
    Rect rect2(rect1);   // 使用rect1复制rect2，此时应该有两个对象  
    system("pause"); 
} 
```

![](/images/posts/C++/28.png)

此时rect1的p和rect2的p各自指向一段内存空间，但它们指向的空间具有相同的内容，这就是所谓的“深拷贝”。

###### 3.防止默认拷贝发生

通过对对象复制的分析，我们发现对象的复制大多在进行“值传递”时发生，这里有一个小技巧可以防止按值传递——**声明一个私有拷贝构造函数**。甚至不必去定义这个拷贝构造函数，这样因为拷贝构造函数是私有的，如果用户试图按值传递或函数返回该类对象，将得到一个编译错误，从而可以避免按值传递或返回对象。

```cpp
#include <iostream>

using namespace std;


class CExample   
{  
private:  
    int a;  
  
public:  
    
    CExample(int b){   //构造函数    
        a = b;  
        cout<<"creat: "<<a<<endl;  
    }  
  
private:  
    //拷贝构造函数私有，类外无法调用，只是声明  
    CExample(const CExample& C);    // 防止按值传递  
  
public:  
    ~CExample(){  
        cout<< "delete: "<<a<<endl;  
    }  
  
    void Show ()  
    {  
        cout<<a<<endl;  
    }  
};  
  
//全局函数  
void g_Fun(CExample C)  
{  
    cout<<"test"<<endl;  
}  
  
int main()  
{  
    CExample test(1);  
    g_Fun(test); //按值传递将出错  
      
    return 0;  
}   
```


##### 拷贝构造函数要注意的细节 

1.拷贝构造函数里能调用private成员变量吗?

解答：拷贝构造函数其时就是一个特殊的构造函数，操作的还是自己类的成员变量，所以不受private的限制。

2.以下函数哪个是拷贝构造函数,为什么?

```cpp
X::X(const X&);      
X::X(X);      
X::X(X&, int a=1);      
X::X(X&, int a=1, int b=2); 
```

解答：对于一个类X, 如果一个构造函数的第一个参数是下列之一:

a) X&

b) const X&

c) volatile X&

d) const volatile X&

且没有其他参数或其他参数都有默认值,那么这个函数是拷贝构造函数.

```cpp
X::X(const X&);              //是拷贝构造函数      
X::X(X&, int=1);             //是拷贝构造函数     
X::X(X&, int a=1, int b=2);  //当然也是拷贝构造函数 
```

3.一个类中可以存在多于一个的拷贝构造函数吗?
 
解答：类中可以存在超过一个拷贝构造函数。

```cpp
class X {   
public:         
  X(const X&);      // const 的拷贝构造  
  X(X&);            // 非const的拷贝构造  
};  
```

注意：如果一个类中只存在一个参数为 X& 的拷贝构造函数,那么就不能使用const X或volatile X的对象实行拷贝初始化。**因为X&是一个普通对象引用，普通对象引用不能用来指向常对象，应当使用常对象引用来指向它，这就相当于普通指针不能指向常变量，应当使用常量指针来指向它**。如：

```cpp
class X {      
public:  
  X();     //构造    
  X(X&);   //拷贝构造 
};      
  
const X cx;  //常对象   
X x = cx;    //error  
```

如果一个类中没有定义拷贝构造函数,那么编译器会自动产生一个默认的拷贝构造函数。这个默认的参数可能为 X::X(const X&)或 X::X(X&),由编译器根据上下文决定选择哪一个。



