---
layout: post
title: 类的静态成员
categories: C和C++基础
description: 类的静态成员
keywords: 类，静态成员
---

如果有n个同类的对象，那么每一个对象都分别有自己的数据成员，不同对象的数据成员各自有值，互不相干。但是有时人们希望有某一个或几个数据成员为所有对象所共有。这样可以实现数据共享。

全局变量，它能够实现数据共享。如果在一个程序文件中有多个函数，在每一个函数中都可以改变全局变量的值，全局变量的值为各函数共享。但是用全局变量的安全性得不到保证，由于在各处都可以自由地修改全局变量的值，很有可能偶一失误，全局变量的值就被修改，导致程序的失败。因此在实际工作中很少使用全局变量。

如果想在同类的多个对象之间实现数据共享，也不要用全局对象，可以用静态的数据成员。

#### 静态数据成员

静态数据成员是一种特殊的数据成员。它以关键字static开头，如：

```cpp
class Box
{
public:
	int volume();
private:
	static int height;   //把height定义为静态的数据成员
	int width;
	int length;
};
```

如果希望各对象中的height的值是一样的，就可以把它定义为静态数据成员，这样它就为各对象所共有，而不只属于某个对象的成员，所有对象都可以引用它。静态的数据成员在内存中只占一份空间。每个对象都可以引用这个静态数据成员。**静态数据成员的值对所有对象都是一样的，如果改变它的值，则在各对象中这个数据成员的值都同时改变了**。这样可以节约空间，提高效率。

说明：

(1).如果只声明了类而未定义对象，**则类的一般数据成员是不占内存空间的，只有在定义对象时，才为对象的数据成员分配空间**。但是**静态数据成员不属于某一个对象，在为对象所分配的空间中不包括静态数据成员所占的空间，静态数据成员是在所有对象之外单独开辟空间**。只要在类中定义了静态数据成员，即使不定义对象，也为静态数据成员分配空间，它可以被引用。在一个类中可以有一个或多个静态数据成员，所有的对象共享这些静态数据成员，都可以引用它。

(2).如果在一个函数中定义了静态变量，在函数结束时该静态变量并不释放，仍然存在并保留其值。静态数据成员也是类似的，它**不随对象的建立而分配空间，也不随对象的撤销而释放(一般数据成员是在对象建立时分配空间，在对象撤销时释放)**。静态数据成员是在程序编译时被分配空间的，到程序结束时才释放空间。

(3).静态数据成员可以初始化，但只能在类体外进行初始化。如：

```cpp
int Box::height=10;   //表示对Box类中的数据成员初始化
```

其一般形式为：

```cpp
数据类型类名::静态数据成员名=初值;
```

不必在初始化语句中加static。

注意：

不能用参数初始化表对静态数据成员初始化。如在定义Box类中这样定义构造函数是错误的：

```cpp
Box(int h,int w,int len):height(h){} //错误，height是静态数据成员
```
如果未对静态数据成员赋初值，则编译系统会自动赋予初值0。

这是因为**静态数据成员先于对象创建之前进行创建和初始化**。


4.静态数据成员既可以通过对象名引用，也可以通过类名来引用。

```cpp
#include <iostream>
using namespace std;
class Box
{
public:
	Box(int,int);
	int volume( );
	static int height;    //把height定义为公用的静态的数据成员
	int width;
	int length;
};

Box::Box(int w,int len)   //通过构造函数对width和length赋初值
{
	width=w;
	length=len;
}

int Box::volume( )
{
	return(height*width*length);
}

int Box::height=10;      //对静态数据成员height初始化

int main( )
{
	Box a(15,20),b(20,30);
	cout<<a.height<<endl;      //通过对象名a引用静态数据成员
	cout<<b.height<<endl;      //通过对象名b引用静态数据成员
	cout<<Box::height<<endl;   //通过类名引用静态数据成员
	cout<<a.volume( )<<endl;   //调用volume函数，计算体积，输出结果
}
```

上面3个输出语句的输出结果相同(都是10)。这就验证了所有对象的静态数据成员实际上是同一个数据成员。

请注意：在上面的程序中将height定义为公用的静态数据成员，所以在类外可以直接引用。**可以看到在类外可以通过对象名引用公用的静态数据成员，也可以通过类名引用静态数据成员。即使没有定义类对象，也可以通过类名引用静态数据成员**。这说明静态数据成员并不是属于对象的，而是属于类的，但类的对象可以引用它。如果静态数据成员被定义为私有的，则不能在类外直接引用，而必须通过公用的成员函数引用。

```cpp
#include <iostream>

using namespace std;

class A{
public:
	A(int i){
		m=i;
	}
	int m;
private:
	static int n;
};

int A::n=10;

int main()
{
	cout<<A::n<<endl;   //因为静态成员n是私有变量 无法在类外进行访问
	system("pause");
}
```

5.有了静态数据成员，各对象之间的数据有了沟通的渠道，实现数据共享，因此可以不使用全局变量。全局变量破坏了封装的原则，不符合面向对象程序的要求。但是也要注意公用静态数据成员与全局变量的不
同，静态数据成员的作用域只限于定义该类的作用域内(如果是在一个函数中定义类，那么其中静态数据成员的作用域就是此函数内)。在此作用域内，可以通过类名和域运算符“::”引用静态数据成员，而不论类对象是否存在。

#### 静态成员函数

成员函数也可以定义为静态的，在类中声明函数的前面加static就成了静态成员函数。如：

```cpp
static int volume();
```

和静态数据成员一样，静态成员函数是类的一部分，而不是对象的一部分。如果要在类外调用公用的静态成员函数，要用类名和域运算符“::”。如：

```cpp
Box::volume();
```

实际上也允许通过对象名调用静态成员函数，如：

```cpp
a.volume();
```

但这并不意味着此函数是属于对象a的，而只是用a的类型而已。

与静态数据成员不同，静态成员函数的作用不是为了对象之间的沟通，而是为了能处理静态数据成员。当调用一个对象的成员函数(非静态成员函数)时，系统会把该对象的起始地址赋给成员函数的this指针。而静态成员函数并不属于某一对象，它与任何对象都无关，因此静态成员函数没有this指针。既然它没有指向某一对象，就无法对一个对象中的非静态成员进行默认访问(即在引用数据成员时不指定对象名)。

因此，静态成员函数与非静态成员函数的根本区别是：**非静态成员函数有this指针，而静态成员函数没有this指针。由此决定了静态成员函数不能访问本类中的非静态成员数据**，但是非静态成员函数可以访问静态成员数据的。

```cpp
#include <iostream>

using namespace std;

class A{
public:
	A(int i){
		m=i;
	}
	int m;

	void print1(){          //非静态成员函数访问可以静态成员数据
		cout<<n<<endl;
	}

	static void print2(){
		cout<<m<<endl;      //静态成员函数无法访问非静态成员数据
	}
private:
	static int n;
};

int A::n=10;

int main()
{
	A a(1);
	a.print();
	system("pause");
}
```

静态成员函数可以直接引用本类中的静态数据成员，因为静态成员同样是属于类的，可以直接引用。在C++程序中，静态成员函数主要用来访问静态数据成员，而不访问非静态成员。假如在一个静态成员函数中有以下语句：

```cpp
cout<<height<<endl;  //若height已声明为static，则引用本类中的静态成员，合法
cout<<width<<endl;   //若width是非静态数据成员，不合法
```

但是，并不是绝对不能引用本类中的非静态成员，只是不能进行默认访问，因为无法知道应该去找哪个对象。如果一定要引用本类的非静态成员，应该加对象名和成员运算符“.”。如：

```cpp
cout<<a.width<<endl; //引用本类对象a中的非静态成员
```

假设a已定义为Box类对象，且在当前作用域内有效，则此语句合法。

静态成员函数的应用：

```cpp
#include <iostream>
using namespace std;
class Student              //定义Student类
{
public:
	Student(int n,int a,float s):num(n),age(a),score(s){} //定义构造函数
	void total();
	static float average();    //声明静态成员函数
private:
	int num;
	int age;
	float score;
	static float sum;          //静态数据成员
	static int count;          //静态数据成员
};

void Student::total(){        //定义非静态成员函数
	sum+=score;               //累加总分
	count++;                  //累计已统计的人数
}

float Student::average(){     //定义静态成员函数
	return(sum/count);
}

float Student::sum=0;         //对静态数据成员初始化
int Student::count=0;         //对静态数据成员初始化

int main( )
{
	Student stud[3]={         //定义对象数组并初始化
	Student(1001,18,70),
	Student(1002,19,78),
	Student(1005,20,98)
	};

	int n;
	cout<<"please input the number of students:";
	cin>>n;                  //输入需要求前面多少名学生的平均成绩
	for(int i=0;i<n;i++)      //调用3次total函数
		stud[i].total();
	cout<<"the average score of"<<n<<"students is"<<Student::average()<<endl;//调用静态成员函数
	system("pause");
}
```

运行结果：

![](/images/posts/C++/29.png)