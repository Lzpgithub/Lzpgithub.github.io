---
layout: post
title: 异常处理
categories: C和C++基础
description: 异常处理
keywords: 异常处理
---

#### 异常处理的任务

程序编制者不仅要考虑程序没有错误的理想情况，更要考虑程序存在错误时的情况，应该能够尽快地发现错误，消除错误。**程序中常见的错误有两大类：语法错误和运行错误**。

在编译时，编译系统能发现程序中的语法错误。有的程序虽然能通过编译，也能投入运行。但是在运行过程中会出现异常，得不到正确的运行结果，甚至导致程序不正常终止，或出现死机现象。这类错误比较隐蔽，不易被发现，往往耗费许多时间和精力。这成为程序调试中的一个难点。

在设计程序时，应当事先分析程序运行时可能出现的各种意外的情况，并且分别制订出相应的处理方法，这就是程序的异常处理的任务。

在运行没有异常处理的程序时，如果运行情况出现异常，由于程序本身不能处理，程序只能终止运行。如果在程序中设置了异常处理机制，则在运行情况出现异常时，由于程序本身已规定了处理的方法，于是程序的流程就转到异常处理代码段处理。用户可以指定进行任何的处理。

需要说明，只要出现与人们期望的情况不同，都可以认为是异常，并对它进行异常处理。因此，所谓异常处理指的是对运行时出现的差错以及其他例外情况的处理。

#### 异常处理的方法

在一个小的程序中，可以用比较简单的方法处理异常。但是在一个大的系统中，如果在每一个函数中都设置处理异常的程序段，会使程序过于复杂和庞大。因此，C++采取的办法是：

如果在执行一个函数过程中出现异常，可以不在本函数中立即处理，而是发出一个信息，传给它的上一级(即调用它的函数)，它的上级捕捉到这个信息后进行处理。如果上一级的函数也不能处理，就再传给其上一级，由其上一级处理。如此逐级上送，如果到最高一级还无法处理，最后只好异常终止程序的执行。

这样做使异常的发现与处理不由同一函数来完成。好处是使底层的函数专门用于解决实际任务，而不必再承担处理异常的任务，以减轻底层函数的负担，而把处理异常的任务上移到某一层去处理。这样可以提高效率。

**C++处理异常的机制是由3个部分组成的，即检查(try)、抛出(throw)和捕捉(catch)**。把需要检查的语句放在try块中，throw用来当出现异常时发出一个异常信息，而catch则用来捕捉异常信息，如果捕捉到了异常信息，就处理它。

例子：给出三角形的三边a，b，c，求三角形的面积。只有a+b>c，b+c>a，c+a>b时才能构成三角形。设置异常处理，对不符合三角形条件的输出警告信息，不予计算。

先写出没有异常处理时的程序：

```cpp
#include <iostream>
#include <cmath>

using namespace std;

int main(){
	double triangle(double,double,double);
	double a,b,c;
	cin>>a>>b>>c;

	while(a>0 && b>0 && c>0){
		cout<<triangle(a,b,c)<<endl;
		cin>>a>>b>>c;
	}
	return 0;
}

double triangle(double a,double b,double c){
	double area;
	double s=(a+b+c)/2;
	area=sqrt(s*(s-a)*(s-b)*(s-c));
	return area;
}
```

修改程序，在函数traingle中对三角形条件进行检查，如果不符合三角形条件，就抛出一个异常信息，在主函数中的try-catch块中调用traingle函数，检测有无异常信息，并作相应处理。修改后的程序如下：

```cpp
#include <iostream>
#include <string>
#include <cmath>

using namespace std;

int main(){
	double triangle(double,double,double);
	double a,b,c;
	cin>>a>>b>>c;

	try{                                    //在try块中包含要检查的函数
		while(a>0 && b>0 && c>0){
			cout<<triangle(a,b,c)<<endl;
			cin>>a>>b>>c;
		}
	}catch(string v){                         //用catch捕捉异常信息并作相应处理
		cout<<v<<"：a="<<a<<",b="<<b<<",c="<<c<<",that is not a triangle!"<<endl;
	}
	cout<<"end"<<endl;
	system("pause");
}

double triangle(double a,double b,double c){ //计算三角形的面积的函数
	string err="Err";
	double s=(a+b+c)/2;
	if (a+b<=c||b+c<=a||c+a<=b) throw err;     //当不符合三角形条件抛出异常信息
	return sqrt(s*(s-a)*(s-b)*(s-c));
}
```

![](/images/posts/C++/153.png)

结合程序分析：

(1)首先把可能出现异常的、需要检查的语句或程序段放在try后面的花括号中。

(2)程序开始运行后，按正常的顺序执行到try块，开始执行try块中花括号内的语句。如果在执行try块内的语句过程中没有发生异常，则catch子句不起作用，流程转到catch子句后面的语句继续执行。

(3)如果在执行try块内的语句(包括其所调用的函数)过程中发生异常，则throw运算符抛出一个异常信息。throw抛出异常信息后，流程立即离开本函数，转到其上一级的函数(main函数)。

throw抛出什么样的数据由程序设计者自定，可以是任何类型的数据。

(4)这个异常信息提供给try-catch结构，系统会寻找与之匹配的catch子句。

(5)在进行异常处理后，程序并不会自动终止，继续执行catch子句后面的语句。由于catch子句是用来处理异常信息的，往往被称为catch异常处理块或catch异常处理器。

异常处理的语法，throw语句一般是由throw运算符和一个数据组成的，其形式为：

```cpp
throw 表达式;
```

try-catch的结构为：

```cpp
try{
	被检查的语句
}catch(异常信息类型 [变量名]){
	进行异常处理的语句
}
```

说明：

(1)被检测的函数必须放在try块中，否则不起作用。

(2)try块和catch块作为一个整体出现，catch块是try-catch结构中的一部分，必须紧跟在try块之后，不能单独使用，在二者之间也不能插入其他语句。但是在一个try-catch结构中，可以只有try块而无catch块。即在本函数中只检查而不处理，把catch处理块放在其他函数中。

(3)try和catch块中必须有用花括号括起来的复合语句，即使花括号内只有一个语句，也不能省略花括号。

(4)一个try-catch结构中只能有一个try块，但却可以有多个catch块，以便与不同的异常信息匹配。

(5)catch后面的圆括号中，一般只写异常信息的类型名，如：

```cpp
catch(double)
```

catch只检查所捕获异常信息的类型，而不检查它们的值。因此如果需要检测多个不同的异常信息，应当由throw抛出不同类型的异常信息。异常信息可以是C++系统预定义的标准类型，也可以是用户自定义的类型(如结构体或类)。如果由throw抛出的信息属于该类型或其子类型，则catch与throw二者匹配，catch捕获该异常信息。catch还可以有另外一种写法，即除了指定类型名外，还指定变量名，如：

```cpp
catch(double d)
```

**此时如果throw抛出的异常信息是double型的变量a，则catch在捕获异常信息a的同时，还使d获得a的值，或者说d得到a的一个拷贝**。什么时候需要这样做呢？有时希望在捕获异常信息时，还能利用throw抛出的值，如：

```cpp
catch(double d){
	cout<<"throw"<<d;
}
```

这时会输出d的值(也就是a值)。当抛出的是类对象时，有时希望在catch块中显示该对象中的某些信息。这时就需要在catch的参数中写出变量名(类对象名)。

(6)如果在catch子句中没有指定异常信息的类型，而用了删节号“**...**”，则表示它可以捕捉任何类型的异常信息，如：

```cpp
catch(...){
	cout<<"OK"<<endl;
}
```

它能捕捉所有类型的异常信息，并输出“OK”。这种catch子句应放在try-catch结构中的最后，相当于“其他”。如果把它作为第一个catch子句，则后面的catch子句都不起作用。

(7)try-catch结构可以与throw出现在同一个函数中，也可以不在同一函数中。当throw抛出异常信息后，首先在本函数中寻找与之匹配的catch，如果在本函数中无try-catch结构或找不到与之匹配的catch，就转到离开出现异常最近的trycatch结构去处理。

(8)在某些情况下，在throw语句中可以不包括表达式，如：

```cpp
throw;
```

表示“我不处理这个异常，请上级处理”。

(9)如果throw抛出的异常信息找不到与之匹配的catch块，那么系统就会调用一个系统函数terminate，使程序终止运行。

在函数嵌套的情况下检测异常处理。这是一个简单的例子，用来说明在try块中有函数嵌套调用的情况下抛出异常和捕捉异常的情况：

```cpp
#include <iostream>

using namespace std;

int main(){
	void f1();
	try{
		f1();                //调用f1()
	}
	catch(double){
		cout<<"OK0!"<<endl;
	}
	cout<<"end0"<<endl;
	system("pause");
}

void f1(){
	void f2();
	try{
		f2();               //调用f2()
	}catch(char){
		cout<<"OK1!"<<endl;
	}
	cout<<"end1"<<endl;
}

void f2(){
	void f3();
	try{
		f3();               //调用f3()
	}catch(int){
		cout<<"Ok2!"<<endl;
	}
	cout<<"end2"<<endl;
}


void f3(){
	double a=0;
	try{
		throw a;            //抛出double类型异常信息
	}catch(float){
		cout<<"OK3!"<<endl;
	}
	cout<<"end3"<<endl;
}
```

程序运行结果：

![](/images/posts/C++/155.png)

![](/images/posts/C++/156.png)

![](/images/posts/C++/157.png)

![](/images/posts/C++/158.png)

#### 在函数声明中进行异常情况指定

为便于阅读程序，使用户在看程序时能够知道所用的函数是否会抛出异常信息以及异常信息可能的类型，C++允许在声明函数时列出可能抛出的异常类型，如：

```cpp
double triangle(double,double,double) throw(double);
```

表示triangle函数只能抛出double类型的异常信息。如果写成：

```cpp
double triangle(double,double,double) throw(int,double,float,char);
```

则表示triangle函数可以抛出int,double,float或char类型的异常信息。异常指定是函数声明的一部分，必须同时出现在函数声明和函数定义的首行中，否则在进行函数的另一次声明时，编译系统会报告“类型不匹配”。

如果在声明函数时未列出可能抛出的异常类型，则该函数可以抛出任何类型的异常信息。

如果想声明一个不能抛出异常的函数，可以写成以下形式:

```cpp
double triangle(double,double,double) throw();       //throw
```

无参数这时即使在函数执行过程中出现了throw语句，实际上也并不执行throw语句，并不抛出任何异常信息，程序将非正常终止。

#### 在异常处理中处理析构函数

如果在try块(或try块中调用的函数)中定义了类对象，在建立该对象时要调用构造函数。在执行try块 (包括在try块中调用其他函数)的过程中如果发生了异常，此时流程立即离开try块。这样流程就有可能离开该对象的作用域而转到其他函数，因而应当事先做好结束对象前的清理工作，C++的异常处理机制会在throw抛出异常信息被catch捕获时，对有关的局部对象进行析构(调用类对象的析构函数)，析构对象的顺序与构造的顺序相反，然后执行与异常信息匹配的catch块中的语句。

在异常处理中处理析构函数：

```cpp
#include <iostream>
#include <string>

using namespace std;

class Student{
public:
	Student(int n,string nam){             //定义构造函数
		cout<<"constructor-"<<n<<endl;
		num=n;
		name=nam;
	}
	~Student(){                            //析构函数
		cout<<"destructor-"<<num<<endl;
	}
	void get_data();                       //成员函数声明
private:
	int num;
	string name;
};


void Student::get_data(){                  //定义成员函数
	if(num==0) 
		throw num;                         //如num=0,抛出int型变量num
	else
		cout<<num<<" "<<name<<endl;        //若num≠0，输出num,name
	cout<<"in get_data()"<<endl;           //输出信息，表示目前在get_data函数中
}


void fun(){
	Student stud1(1101,"Tan");             //建立对象stud1
	stud1.get_data();                      //调用stud1的get_data函数
	Student stud2(0,"Li");                 //建立对象stud2
	stud2.get_data();                      //调用stud2的get_data函数
}


int main(){
	cout<<"main begin"<<endl;              //表示主函数开始了
	cout<<"call fun()"<<endl;              //表示调用fun函数
	try{
		fun();                             //调用fun函数
	}catch(int n){                         //表示num=0出错
		cout<<"num="<<n<<",error!"<<endl;
	}
	cout<<"main end"<<endl;                //表示主函数结束
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/159.png)