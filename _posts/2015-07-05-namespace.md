---
layout: post
title: 命名空间
categories: C和C++基础
description: 命名空间
keywords: 命名空间
---

#### 为什么需要命名空间

```cpp
using namespace std;
```

这就是使用了命名空间std。

**命名空间是ANSI C++引入的可以由用户命名的作用域，用来处理程序中常见的同名冲突。**在C语言中定义了3个层次的作用域，即文件(编译单元)、函数和复合语句。C++又引入了类作用域，类是出现在文件内的。在不同的作用域中可以定义相同名字的变量，互不干扰，系统能够区别它们。

如果在文件中定义了两个类，在这两个类中可以有同名的函数。在引用时，为了区别，应该加上类名作为限定，这样才不会发生混淆。如：

```cpp
class A{             //声明A类
public:
	void fun1();     //声明A类中的fun1函数
private:
	int i;
};

void A::fun1(){      //定义A类中的fun1函数
    //
}

class B{             //声明B类
public:
	void fun1();     //B类中也有fun1函数
	void fun2();
};

void B::fun1(){      //定义B类中的fun1函数
	//
}
```

在文件中可以定义全局变量(global variable)，它的作用域是整个程序。如果在文件A中定义了一个变
量a：

```cpp
int a=3;
```

在文件B中可以再定义一个变量a：

```cpp
int a=5;
```

在分别对文件A和文件B进行编译时不会有问题。但是，如果一个程序包括文件A和文件B，那么在进行连接时，会报告出错，因为在同一个程序中有两个同名的变量，认为是对变量的重复定义。问题在于全局变量的作用域是整个程序，在同一作用域中不应有两个或多个同名的实体(entity)，包括变量、函数和类等。

可以通过extern声明同一程序中的两个文件中的同名变量是同一个变量。如果在文件B中有以下声明：

```cpp
extern int a;
```

表示文件B中的变量a是在其他文件中已定义的变量。由于有此声明，在程序编译和连接后，文件A的变
量a的作用域扩展到了文件B。如果在文件B中不再对a赋值，则在文件B中用以下语句输出的是文件A中变量a：

```cpp
cout<<a;      //得到a的值为3
```

在简单的程序设计中，只要人们小心注意，可以争取不发生错误。但是，**一个大型的应用软件，往往不是由一个人独立完成的，假如不同的人分别定义了类，放在不同的头文件中，在主文件(包含主函数的文件)需要用这些类时就用#include命令行将这些头文件包含进来。由于各头文件是由不同的人设计的，有可能在不同的头文件中用了相同的名字来命名所定义的类或函数。这样在程序中就会出现名字冲突。**

例如程序员甲在头文件header1.h中定义了类Student和函数fun：

```cpp
//header1.h (头文件1 设其文件名为cc14-4-h1.h)
#include <iostream>
#include <string>
#include <cmath>

using namespace std;

class Student{                    //声明Student类
public:
	Student(int n,string nam,char s){
		num=n;
		name=nam;
		sex=s;
	}
	void get_data();
private:
	int num;
	string name;
	char sex;
};

void Student::get_data(){         //成员函数定义
	cout<<num<<" "<<name<<" "<<sex<<endl;
}

double fun(double a,double b){    //定义全局函数(即外部函数)
	return sqrt(a+b);
}
```

在main函数所在的文件中包含头文件header1.h：

```cpp
#include <iostream>
#include "cc14-4-h1.h"
//注意要用双引号，因为文件一般是放在用户目录中的
using namespace std;

int main(){
	Student stud1(101,"Wang",18); //定义类对象stud1
	stud1.get_data();
	cout<<fun(5,3)<<endl;
	return 0;
}
```

程序能正常运行输出。

如果程序员乙写了头文件header2.h，在其中除了定义其他类以外，还定义了类Student和函数fun，但其内容与头文件header1.h中的Student和函数fun有所不同。

```cpp
//header2.h (头文件2 设其文件名为cc14-4-h2.h)
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

class Student{                          //声明Student类
public:
	Student(int n,string nam,char s){   //参数与header1中的student不同
		num=n;
		name=nam;
		sex=s;
	}
	void get_data();
private:
	int num;
	string name;
	char sex;                           //此项与header1不同
};

void Student::get_data(){               //成员函数定义
	cout<<num<<" "<<name<<" "<<sex<<endl;
}

double fun(double a,double b){          //定义全局函数
	return sqrt(a-b);                   //返回值与header1中的fun函数不同
} 

//头文件2中可能还有其他内容
```

假如主程序员在其程序中要用到header1.h中的Student和函数fun，因而在程序中包含了头文件header1.h，同时要用到头文件header2.h中的一些内容，因而在程序中又包含了头文件header2.h。如果主文件(包含主函数的文件)如下：

```cpp
//main file
#include <iostream>
#include "cc14-4-h1.h"     //包含头文件1
#include "cc14-4-h2.h"     //包含头文件2

using namespace std;

int main(){
	Student stud1(101,"Wang",18);
	stud1.get_data();
	cout<<fun(5,3)<<endl;
	return 0;
}
```

这时程序编译就会出错。因为在预编译后，头文件中的内容取代了对应的#include命令行，这样就在同一个程序文件中出现了两个Student类和两个fun函数，显然是重复定义，这就是名字冲突，即在同一个作用域中有两个或多个同名的实体。

不仅如此，在程序中还往往需要引用一些库，为此需要包含有关的头文件。如果在这些库中包含有与程序的全局实体同名的实体，或者不同的库中有相同的实体名，则在编译时就会出现名字冲突。为了避免这类问题的出现，人们提出了许多方法，
例如：

将实体的名字写得长一些；把名字起得特殊一些，包括一些特殊的字符；由编译系统提供的内部全局标识符都用下划线作为前缀，如_complex()，以避免与用户命名的实体同名；由软件开发商提供的实体的名字用特定的字符作为前缀。但是这样的效果并不理想，而且增加了阅读程序的难度，可读性降低了。

C语言和早期的C++语言没有提供有效的机制来解决这个问题，没有使库的提供者能够建立自己的命名空间的工具。人们希望ANSI C++标准能够解决这个问题，提供一种机制、一种工具，使由库的设计者命名的全局标识符能够和程序的全局实体名以及其他库的全局标识符区别开来。


#### 什么是命名空间

为了解决上面这个问题，ANSI C++增加了命名空间(namespace)。所谓命名空间，实际上就是一个由程序设计者命名的内存区域。程序设计者可以根据需要指定一些有名字的空间域，把一些全局实体分别放在各个命名空间中，从而与其他全局实体分隔开来。如：

```cpp
namespace ns1      //指定命名空间ns1
{
	int a;
	double b;
}
````

现在命名空间成员包括变量a和b，注意a和b仍然是全局变量，仅仅是把它们隐藏在指定的命名空间中而已。

如果在程序中要使用变量a和b，必须加上命名空间名和作用域分辨符“：：”，如ns1：：a，ns1：：b。这种用法称为命名空间限定(qualified)，这些名字(如ns1：：a)称为被限定名(qualified name)。C++中命名空间的作用类似于操作系统中的目录和文件的关系。命名空间的作用是建立一些互相分隔的作用域，把一些全局实体分隔开来，以免产生名字冲突。可以根据需要设置许多个命名空间，每个命名空间名代表一个不同的命名空间域，不同的命名空间不能同名。这样，可以把不同的库中的实体放到不同的命名空间中。过去我们用的全局变量可以理解为全局命名空间，独立于所有有名的命名空间之外，它是不需要用namespace声明的，实际上是由系统隐式声明的，存在于每个程序之中。

在声明一个命名空间时，花括号内不仅可以包括变量，而且还可以包括以下类型：

•变量(可以带有初始化)；

•常量；

•函数(可以是定义或声明)；

•结构体；

•类；

•模板；

•命名空间(在一个命名空间中又定义一个命名空间，即嵌套的命名空间)。

例如：

```cpp
namespace ns1{
	const int RATE=0.08;   //常量
	double pay;            //变量
	double tax(){          //函数
		return a*RATE;
	}
	namespace ns2{         //嵌套的命名空间
		int age;
	}
}
```

如果想输出命名空间ns1中成员的数据，可以采用下面的方法：

```cpp
cout<<ns1::RATE<<endl;
cout<<ns1::pay<<endl;
cout<<ns1::tax()<<endl;
cout<<ns1::ns2::age<<endl;   //需要指定外层的和内层的命名空间名
```

#### 使用命名空间解决名字冲突

修改两个头文件，把在头文件中声明的类分别放在两个不同的命名空间中。

```cpp
//header1.h (头文件1 设其文件名为cc14-4-h1.h)
#include <iostream>
#include <string>
#include <cmath>

using namespace std;

namespace ns1{                        //声明命名空间ns1
	class Student{                    //声明Student类
	public:
		Student(int n,string nam,char s){
			num=n;
			name=nam;
			sex=s;
		}
		void get_data();
	private:
		int num;
		string name;
		char sex;
	};

	void Student::get_data(){         //成员函数定义
		cout<<num<<" "<<name<<" "<<sex<<endl;
	}

	double fun(double a,double b){    //定义全局函数(即外部函数)
		return sqrt(a+b);
	}
}
```

```cpp
//header2.h (头文件2 设其文件名为cc14-4-h2.h)
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

namespace ns2{                              //声明命名空间ns2
	class Student{                          //声明Student类
	public:
		Student(int n,string nam,char s){   //参数与header1中的student不同
			num=n;
			name=nam;
			sex=s;
		}
		void get_data();
	private:
		int num;
		string name;
		char sex;                           //此项与header1不同
	};
	
	void Student::get_data(){               //成员函数定义
		cout<<num<<" "<<name<<" "<<sex<<endl;
	}
	
	double fun(double a,double b){          //定义全局函数
		return sqrt(a-b);                   //返回值与header1中的fun函数不同
	} 
	
	//头文件2中可能还有其他内容
}
```

```cpp
//main file(主文件)
#include <iostream>
#include "cc14-5-h1.h"                   //包含头文件1
#include "cc14-5-h2.h"                   //包含头文件2
using namespace std;
int main(){
	ns1::Student stud1(101,"Wang",18);   //用命名空间ns1中声明的Student类定义stu1
	stud1.get_data();                    //不要写成ns1::stud1.get_data();
	cout<<ns1::fun(5,3)<<endl;           //调用命名空间ns1中的fun函数
	ns2::Student stud2(102,"Li",'f');    //用命名空间ns2中声明的Student类定义stud2
	stud2.get_data();
	cout<<ns2::fun(5,3)<<endl;           //调用命名空间ns1中的fun函数
	return 0;
}
```

#### 使用命名空间成员的方法

在引用命名空间成员时，要用命名空间名和作用域分辨符对命名空间成员进行限定，以区别不同的命名空间中的同名标识符。即：

```cpp
命名空间名：：命名空间成员名
```

这种方法是有效的，能保证所引用的实体有惟一的名字。但是如果命名空间名字比较长，尤其在有命名空间嵌套的情况下，为引用一个实体，需要写很长的名字。在一个程序中可能要多次引用命名空间成员，就会感到很不方便。为此，C++提供了一些机制，能简化使用命名空间成员的手续。

（1）使用命名空间别名可以为命名空间起一个别名(namespace alias)，用来代替较长的命名空间名。如：

```cpp
namespace Television     //声明命名空间，名为Television
{
	...
}
```

可以用一个较短而易记的别名代替它。如：

```cpp
namespace TV = Television;   //别名TV与原名Television等价
```

（2）使用“using 命名空间成员名”，using后面的命名空间成员名必须是由命名空间限定的名字。例如：

```cpp
using ns1::Student;
```

using声明的有效范围是从using语句开始到using所在的作用域结束。如果在以上的using语句之后有以下语句：

```cpp
Student stud1(101,"Wang",18);   //此处的Student相当于ns1::Student stud1(101,"Wang",18)
```

又如：

```cpp
using ns1::fun;          //声明其后出现的fun是属于命名空间ns1中的fun
cout<<fun(5,3)<<endl;    //此处的fun函数相当于ns1::fun(5,3)
```

显然，这可以避免在每一次引用命名空间成员时都用命名空间限定，使得引用命名空间成员方便易用。但是要注意：在同一作用域中用using声明的不同命名空间的成员中不能有同名的成员。例如：

```cpp
using ns1::Student;      //声明其后出现的Student是命名空间ns1中的Student
using ns2::Student;      //声明其后出现的Student是命名空间ns2中的Student
Student stud1;           //请问此处的Student是哪个命名空间中的Student?
```

于是产生了二义性，编译出错。

（3）使用“using namespace命名空间名”能否在程序中用一个语句就能一次声明一个命名空间中的全部成员呢？

C++提供了“using namespace”语句来实现这一目的，其一般格式是：

```cpp
using namespace ns1;
```

声明了在本作用域中要用到命名空间ns1中的成员，在使用该命名空间的任何成员时都不必用命名空间限定。如果在作了上面的声明后有以下语句：

```cpp
Student stud1(101,"Wang",18);   //Student隐含指命名空间ns1中的Student
cout<<fun(5,3)<<endl;           //这里的fun函数时命名空间ns1中fun函数
```

在用using namespace声明的作用域中，命名空间ns1的成员就好像在全局域声明的一样。因此可以不必用命名空间限定。显然这样的处理对写程序比较方便。但是如果同时用using namespace声明多个命名空间时，往往容易出错。因此只有在使用命名空间数量很少，以及确保这些命名空间中没有同名成员时才用using namespace语句。

#### 无名的命名空间

C++还允许使用没有名字的命名空间，如在文件A中声明了以下的无名命名空间：

```cpp
namespace{              //命名空间没有名字
	void fun(){         //定义命名空间成员
		cout<<"OK."<<endl;
	}
}
```

由于命名空间没有名字，在其他文件中显然无法引用，它只在本文件的作用域内有效。无名命名空间的成员fun函数的作用域为文件A。在文件A中使用无名命名空间的成员，不必(也无法)用命名空间名限定。如果在文件A中有以下语句：

```cpp
fun();
```

则执行无名命名空间中的成员fun函数，输出“OK.”。在本程序中的其他文件中也无法使用该fun函数，也就是把fun函数的作用域限制在本文件范围中。

#### 标准命名空间std

为了解决C++标准库中的标识符与程序中的全局标识符之间以及不同库中的标识符之间的同名冲突，应该将不同库的标识符在不同的命名空间中定义(或声明)。标准C++库的所有的标识符都是在一个名为std的命名空间中定义的，或者说标准头文件(如iostream)中函数、类、对象和类模板是在命名空间std中定义的。这样，在程序中用到C++标准库时，需要使用std作为限定。如：

```cpp
std::cout<<"OK."<<endl;     //声明cout是在命名空间std中定义的流对象
```

在大多数的C++程序中常用using namespace语句对命名空间std进行声明，这样可以不必对每个命名空间成员一一进行处理，在文件的开头加入以下using namespace声明：

```cpp
using namespace std;
```

这样，在std中定义和声明的所有标识符在本文件中都可以作为全局量来使用。但是应当绝对保证在程序中不出现与命名空间std的成员同名的标识符。由于在命名空间std中定义的实体实在太多，有时程序设计人员也弄不清哪些标识符已在命名空间std中定义过，为减少出错机会，有的专业人员喜欢用若干个“using命名空间成员”声明来代替“using namespace命名空间”声明，如：

```cpp
using std::string;
using std::cout;
using std::cin;
```

为了减少在每一个程序中都要重复书写以上的using声明，程序开发者往往把编写应用程序时经常会用到的命名空间std成员的using声明组成一个头文件，然后在程序中包含此头文件即可。


#### 使用早期的函数库

C语言程序中各种功能基本上都是由函数来实现的，在C语言的发展过程中建立了功能丰富的函数库，C++从C语言继承了这份宝贵的财富。在C++程序中可以使用C语言的函数库。如果要用函数库中的函数，就必须在程序文件中包含有关的头文件，在不同的头文件中，包含了不同的函数的声明。在C++中使用这些头文件有两种方法。

(1)用C语言的传统方法。头文件名包括后缀.h，如stdio.h，math.h等。由于C语言没有命名空间，头文件并不存放在命名空间中，因此在C++程序文件中如果用到带后缀.h的头文件时，不必用命名空间。只需在文件中包含所用的头文件即可。如：

```cpp
#include <math.h>
```

(2)用C++的新方法。C++标准要求系统提供的头文件不包括后缀.h，例如iostream、string。为了表示与C语言的头文件有联系又有区别，C++所用的头文件名是在C语言的相应的头文件名(但不包括后缀.h)之前加一字母c。此外，由于这些函数都是在命名空间std中声明的，因此在程序中要对命名空间std作声明。如：

```cpp
#include <cstdio>
#include <cmath>
using namespace std;
```

目前所用的大多数C++编译系统既保留了C的用法，又提供了C++的新方法。下面两种用法等价，可以任选：

C传统方法：

```cpp
#include <stdio.h>
#include <math.h>
#include <string.h>
```

C++新方法：

```cpp
#include <cstdio>
#include <cmath>
#include <cstring>
using namespace std;
```



