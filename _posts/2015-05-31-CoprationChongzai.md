---
layout: post
title: 运算符重载
categories: C和C++基础
description: 运算符重载
keywords: 运算符重载
---

#### 什么是运算符重载

所谓重载，就是重新赋予新的含义。函数重载就是对一个已有的函数赋予新的含义，使之实现新功能。运算符也可以重载。实际上，我们已经在不知不觉之中使用了运算符重载。

用户能否根据自己的需要对C++已提供的运算符进行重载，赋予它们新的含义，使之一名多用。譬如，能否用“+”号进行两个复数的相加。

用户必须自己设法实现复数相加。例如用户可以通过定义一个专门的函数来实现复数相加：

```cpp
#include <iostream>
using namespace std;
class Complex //定义Complex类
{
public:
	Complex( ){real=0;imag=0;}                 //定义构造函数
	Complex(double r,double i){real=r;imag=i;} //构造函数重载
	Complex complex_add(Complex &c2);          //声明复数相加函数
	void display( );                           //声明输出函数
private:
	double real;     //实部
	double imag;     //虚部
};

Complex Complex::complex_add(Complex &c2)
{
	Complex c;
	c.real=real+c2.real;
	c.imag=imag+c2.imag;
	return c;
}

void Complex::display( )    //定义输出函数
{
	cout<<"("<<real<<","<<imag<<"i)"<<endl;
}

int main( )
{
	Complex c1(3,4),c2(5,-10),c3; //定义3个复数对象
	c3=c1.complex_add(c2);        //调用复数相加函数
	cout<<"c1="; 
	c1.display( );                //输出c1的值
	cout<<"c2="; 
	c2.display( );                //输出c2的值
	cout<<"c1+c2="; 
	c3.display( );                //输出c3的值
	system("pause");
}
```

运行结果：

![](/images/posts/C++/30.png)

结果无疑是正确的，但调用方式不直观、太烦琐，使人感到很不方便。能否也和整数的加法运算一样，直接用加号“+”来实现复数运算呢？如c3=c1+c2，编译系统就会自动完成c1和c2两个复数相加的运算。如果能做到，就为对象的运算提供了很大的方便。这就需要对运算符“+”进行重载。

#### 运算符重载的方法

运算符重载的方法是定义一个重载运算符的函数，在需要执行被重载的运算符时，系统就自动调用该函数，以实现相应的运算。也就是说，运算符重载是通过定义函数实现的。运算符重载实质上是函数的重载。

重载运算符的函数一般格式如下：

函数类型 operator 运算符名称 (形参表列){对运算符的重载处理}

例如，想将“+”用于Complex类(复数)的加法运算，函数的原型可以是这样的：

```cpp
Complex operator+ (Complex& c1,Complex& c2);
```

在定义了重载运算符的函数后，可以说：函数operator+重载了运算符+。为了说明在运算符重载后，执行表达式就是调用函数的过程，可以把两个整数相加也想像为调用下面的函数：

```cpp
int operator + (int a,int b)
{
	return (a+b);
}
````

以下例子中就是重载运算符“+”，使之用于复数相加：

```cpp
#include <iostream>

using namespace std;

class Complex
{public:
	Complex(){
		real=0;imag=0;
	}

	Complex(double r,double i){
		real=r;imag=i;
	}

	Complex operator+(Complex &c2);             //声明重载运算符的函数
	void display();

private:
	double real;
	double imag;
};

Complex Complex::operator+(Complex &c2)         //定义重载运算符的函数
{
	Complex c;
	c.real=real+c2.real;
	c.imag=imag+c2.imag;
	return c;
}

void Complex::display()
{
	cout<<"("<<real<<","<<imag<<"i)"<<endl;
}

int main()
{ 
	Complex c1(3,4),c2(5,-10),c3;
	c3=c1+c2;                      //运算符+用于复数运算
	cout<<"c1=";
	c1.display();
	cout<<"c2=";
	c2.display();
	cout<<"c1+c2=";
	c3.display();
	system("pause");
}
```

其中c1+c2可以解释为：

```cpp
c1.operator+(c2);
```

对上面的运算符重载函数operator+还可以改写得更简练一些：

```cpp
Complex Complex::operator+(Complex &c2)
{
	return Complex(real+c2.real,imag+c2.imag);
}
```

需要说明的是：**运算符被重载后，其原有的功能仍然保留，没有丧失或改变**。

通过运算符重载，扩大了C++已有运算符的作用范围，使之能用于类对象。运算符重载对C++有重要的意义，把运算符重载和类结合起来，可以在C++程序中定义出很有实用意义而使用方便的新的数据类型。运算符重载使C++具有更强大的功能、更好的可扩充性和适应性，这是C++最吸引人的特点之一。

#### 重载运算符的规则

C++不允许用户自己定义新的运算符，只能对已有的C++运算符进行重载。

C++允许重载的运算符：C++中绝大部分的运算符允许重载。

不能重载的运算符只有5个：

![](/images/posts/C++/98.png)

前两个运算符不能重载是为了保证访问成员的功能不能被改变，域运算符和sizeof运算符的运算对象是类型而不是变量或一般表达式，不具重载的特征。

**重载不能改变运算符运算对象(即操作数)的个数；重载不能改变运算符的优先级别；重载不能改变运算符的结合性；重载运算符的函数不能有默认的参数，否则就改变了运算符参数的个数**。

用于类对象的运算符一般必须重载，但有两个例外，运算符“=”和“&”不必用户重载：

1.赋值运算符(=)可以用于每一个类对象，可以利用它在同类对象之间相互赋值。

2.地址运算符&也不必重载，它能返回类对象在内存中的起始地址。

运算符重载函数可以是类的成员函数，也可以是类的友元函数，还可以是既非类的成员函数也不是友元函数的普通函数。

#### 运算符重载函数作为类成员函数和友元函数

在上面例子中对运算符“+”进行了重载，使之能用于两个复数的相加。在该例中运算符重载函数operator+作为Complex类中的成员函数。实际上，**运算符重载函数有两个参数，由于重载函数是Complex类中的成员函数，有一个参数时隐含的，运算符函数时用this指针隐式地访问类对象的成员**。

重载函数operator+访问了两个对象中的成员，一个是this指针指向的对象中的成员，一个是形参对象中的成员。如**this->real+c2.real**，this->real就是c1.real。

在将运算符函数重载为成员函数后，如果出现含该运算符的表达式，如c1+c2，编译系统就把它解释为：

```cpp
c1.operator+(c2);
```

即通过对象c1调用运算符重载函数，并以表达式中第二个参数(运算符右侧的类对象c2)作为函数实参。运算符重载函数的返回值是Complex类型，返回值是复数c1和c2之和**(Complex(c1.real +c2.real,c1.imag+c2.imag))**。

运算符重载函数除了可以作为类的成员函数外，还可以是非成员函数：

将运算符“+”重载为适用于复数加法，重载函数不作为成员函数，而放在类外，作为Complex类的友元函数：

```cpp
#include <iostream>

using namespace std;

class Complex
{public:
	Complex(){
		real=0;imag=0;
	}

	Complex(double r,double i){
		real=r;imag=i;
	}

	friend Complex operator+(Complex &c1,Complex &c2);     //重载函数作为友元函数
	void display();

private:
	double real;
	double imag;
};

Complex operator+(Complex &c1,Complex &c2)     //定义重载运算符的函数
{
	Complex c;
	c.real=c1.real+c2.real;
	c.imag=c1.imag+c2.imag;
	return c;
}

void Complex::display()
{
	cout<<"("<<real<<","<<imag<<"i)"<<endl;
}

int main()
{ 
	Complex c1(3,4),c2(5,-10),c3;
	c3=c1+c2;                                 //运算符+用于复数运算
	cout<<"c1=";
	c1.display();
	cout<<"c2=";
	c2.display();
	cout<<"c1+c2=";
	c3.display();
	system("pause");
}
```

如想将一个复数和一个整数相加，如c1+i,可以将运算符重载函数作为成员函数，如下面的形式：

```cpp
//运算符重载函数作Complex类的成员函数
Complex Complex::operator+(int &i) 
{
	return Complex(real+i,imag);
}
```

注意在表达式中重载的运算符“+”左侧应为Complex类的对象，如：

```cpp
c3=c2+i;
```

不能写成：

```cpp
c3=i+c2;
```

如果要求在使用重载运算符时运算符左侧的操作数是整型量，这时是无法利用前面定义的重载运算符的，因为无法调用i.operator+函数。可想而知，如果运算符左侧的操作数属于C++标准类型(如int)或是一个其他类的对象，则运算符重载函数不能作为成员函数，只能作为非成员函数。如果函数需要访问类的私有成员，则必须声明为友元函数。可以在Complex类中声明：

```cpp
friend Complex operator+(int &i,Complex &c); //第一个参数可以不是类对象
```

在类外定义友元函数：

```cpp
Complex operator+(int &i, Complex &c)        //运算符重载函数不是成员函数
{
	return Complex(i+c.real,c.imag);
}
```

**将双目运算符重载为友元函数时，在函数的形参表列中必须有两个参数，不能省略，形参的顺序任意，不要求第一个参数必须为类对象。但在使用运算符的表达式中，要求运算符左侧的操作数与函数第一个参数对应，运算符右侧的操作数与函数的第二个参数对应**。如：

```cpp
c3=i+c2;    //正确，类型匹配
c3=c2+i;    //错误，类型不匹配
```

请注意，数学上的交换律在此不适用。如果希望适用交换律，则应再重载一次运算符“+”。如：

```cpp
Complex operator+(Complex &c,int&i)    //此时第一个参数为类对象
{
	return Complex(i+c.real,c.imag);
}
```

这样，使用表达式i+c2和c2+i都合法，编译系统会根据表达式的形式选择调用与之匹配的运算符重载函数。

由于友元的使用会破坏类的封装，因此从原则上说，要尽量将运算符函数作为成员函数。但考虑到各方面的因素，一般将单目运算符重载为成员函数，将双目运算符重载为友元函数。

#### 重载双目运算符

双目运算符(或称二元运算符)是C++中最常用的运算符。双目运算符有两个操作数，通常在运算符的左右两侧，如3+5，a=b，i<10等。在重载双目运算符时，不言而喻在函数中应该有两个参数。

定义一个字符串类String，用来存放不定长的字符串，重载运算符“==”,“<”和“>”，用于两个字符串的等于、小于和大于的比较运算：

```cpp
#include <iostream>

using namespace std;

class String{
public:
	String( ){p=NULL;}
	String(char*str);
	friend bool operator>(String &string1,String &string2);
	friend bool operator<(String &string1,String &string2);
	friend bool operator==(String &string1,String &string2);
	void display( );
private:
	char *p;
};

String::String(char*str){
	p=str;
}

void String::display( )                           //输出p所指向的字符串
{
	cout<<p;
}

bool operator>(String &string1,String &string2){   //大于号运算符重载
	if(strcmp(string1.p,string2.p)>0)
		return true;
	else
		return false;
}

bool operator<(String &string1,String &string2){   //小于号运算符重载
	if(strcmp(string1.p,string2.p)<0)
		return true;
	else
		return false;
}

bool operator==(String &string1,String &string2){  //等于号运算符重载
	if(strcmp(string1.p,string2.p)==0)
		return true;
	else
		return false;
}

void compare(String &string1,String &string2){
	if(operator>(string1,string2)==1){
		string1.display( );
		cout<<" > ";
		string2.display( );}
	else
		if(operator<(string1,string2)==1){
			string1.display( );
			cout<<" < ";
			string2.display( );
		}
		else
			if(operator==(string1,string2)==1){
				string1.display( );
				cout<<" = ";
				string2.display( );
			}
			cout<<endl;
}


int main( )
{
	String string1("Hello"),string2("Book"),string3("Computer"),string4("Hello");
	compare(string1,string2);
	compare(string2,string3);
	compare(string1,string4);
	system("pause");
}
```

程序运行结果为：

![](/images/posts/C++/99.png)

#### 重载单目运算符

单目运算符只有一个操作数，如!a，-b，&c，*p，还有最常用的++i和--i等。重载单目运算符的方法与重载双目运算符的方法是类似的。但由于单目运算符只有一个操作数，因此运算符重载函数只有一个参数，如果运算符重载函数作为成员函数，则还可省略此参数。

“++”和“--”运算符有两种使用方式，前置自增运算符和后置自增运算符，它们的作用是不一样的，在重载时怎样区别这二者呢？

**C++约定：在自增(自减)运算符重载函数中，增加一个int型形参，就是后置自增(自减)运算符函数**。

```cpp
#include <iostream>

using namespace std;

class Time{
public:
	Time(){minute=0;sec=0;}                  //默认构造函数
	Time(int m,int s):minute(m),sec(s){}     //构造函数重载
	Time operator++();                       //声明前置自增运算符“++”重载函数
	Time operator++(int);                    //声明后置自增运算符“++”重载函数
	void display(){
	cout<<minute<<":"<<sec<<endl;
	}
private:
	int minute;
	int sec;
};

Time Time::operator++()                      //定义前置自增运算符“++”重载函数
{
	if(++sec>=60){
		sec-=60;
		++minute;
	}
	return *this;                            //返回自加后的当前对象
}


Time Time::operator++(int)                  //定义后置自增运算符“++”重载函数
{
	Time temp(*this);
    sec++;
	if(sec>=60){
		sec-=60;
		++minute;
	}
	return temp;                            //返回的是自加前的对象
}


int main(){
	Time time1(34,59),time2;
	cout<<"time1 :";
	time1.display( );
	++time1;
	cout<<"++time1:";
	time1.display( );
	time2=time1++;          //将自加前的对象的值赋给time2
	cout<<"time1++:";
	time1.display( );
	cout<<"time2 :";
	time2.display( );       //输出time2对象的值
	system("pause");
}
```

![](/images/posts/C++/100.png)


#### 重载流插入运算符和流提取运算符

C++的流插入运算符“<<”和流提取运算符“>>”是C++在类库中提供的，所有C++编译系统都在类库中提供输入流类istream和输出流类ostream。**cin和cout分别是istream类和ostream类的对象。“<<”和“>>”相当于把后面对应的内容插入到流对象cin和cout中**，在类库提供的头文件中已经对“<<”和“>>”进行了重载，使之作为流插入运算符和流提取运算符，能用来输出和输入C++标准类型的数据。因此，在本书前面几章中，凡是用“cout<<”和“cin>>”对标准类型数据进行输入输出的，都要用#include <iostream>把头文件包含到本程序文件中。


用户自己定义的类型的数据，是不能直接用“<<”和“>>”来输出和输入的。如果想用它们输出和输入自己声明的类型的数据，必须对它们重载。对“<<”和“>>”重载的函数形式如下：

```cpp
istream &operator>>(istream&,自定义类 &);
ostream& operator<<(ostream&,自定义类 &);
```

即重载运算符“>>”的函数的第一个参数和函数的类型都必须是istream&类型，第二个参数是要进行输入操作的类。重载“<<”的函数的第一个参数和函数的类型都必须是ostream&类型，第二个参数是要进行输出操作的类。因此，只能将重载“>>”和“<<”的函数作为友元函数或普通的函数，而不能将它们定义为成员函数。

##### 重载流插入运算符“<<”

用重载的“<<”输出复数：

```cpp
#
include <iostream>
using namespace std;
class Complex
{public:
	Complex( ){real=0;imag=0;}
	Complex(double r,double i){real=r;imag=i;}
	Complex operator + (Complex &c2);                //运算符“+”重载为成员函数
	friend ostream &operator<<(ostream&,Complex&);   //运算符“<<”重载为友元函数
private:
	double real;
	double imag;
};

Complex Complex::operator+(Complex &c2)              //定义运算符“+”重载函数
{
	return Complex(real+c2.real,imag+c2.imag);
}

ostream &operator<<(ostream& output,Complex& c)      //定义运算符“<<”重载函数
{
	output<<"("<<c.real<<"+"<<c.imag<<"i)"<<endl;
	return output;
}

int main(){
	Complex c1(2,4),c2(6,10),c3;
	c3=c1+c2;
	cout<<c3;
	return 0;
}
```

可以看到在对运算符“<<”重载后，在程序中用“<<”不仅能输出标准类型数据，而且可以输出用户自己定义的类对象。由于已将运算符“<<”的重载函数声明为Complex类的友元函数，编译系统把“cout<<c3”解释为：

```cpp
operator<<(cout,c3);
```

即以cout和c3作为实参，调用operator<<函数，调用函数时，形参output成为cout的引用，形参c成为c3的引用。因此调用函数的过程相当于执行：

```cpp
cout<<"("<<c3.real<<"+"<<c3.imag<<"i)"<<endl; 
return cout;
```

上一行中的“<<”是C++预定义的流插入符，因为它右侧的操作数是字符串常量和double型数据。执行cout语句输出复数形式的信息。然后执行return语句。

**请思考：return output的作用是什么？**

回答：能连续向输出流插入信息。output是ostream类的对象，它是实参cout的引用，也就是cout通过传送地址给output，使它们共享同一段存储单元，或者说output是cout的别名。因此return output就是return cout，将输出流cout的现状返回，即保留输出流的现状。

如：

```cpp
cout<<c3<<c2;
```

先处理cout<<c3，即：

```cpp
(cout<<c3)<<c2;
```

而执行(cout<<c3)得到的结果就是具有新内容的流对象cout，因此，(cout<<c3)<<c2相当于cout(新值)<<c2。**运算符“<<”左侧是ostream类对象cout，右侧是Complex类对象c2，则再次调用运算符“<<”重载函数，接着向输出流插入c2的数据。**

C++规定运算符“<<”重载函数的第一个参数和函数的类型都必须是ostream类型的引用，就是为了返回cout的当前值以便连续输出。

注意区分什么情况下的“<<”是标准类型数据的流插入符，什么情况下的“<<”是重载的流插入符。比如：

```cpp
cout<<c3<<5<<endl;
```

第一个是调用重载的流插入符，后面两个“<<”不是重载的流插入符，因为它的右侧不是Complex类对象而是标准类型的数据，是用预定义的流插入符处理的。

##### 重载流提取运算符“>>”

C++预定义的运算符“>>”的作用是从一个输入流中提取数据，如“cin>>i;”表示从输入流中提取一个整数赋给变量i(假设已定义i为int型)。重载流提取运算符的目的是希望将“>>”用于输入自定义类型的对象的信息。

```cpp
#include <iostream>
using namespace std;
class Complex
{public:
	friend ostream &operator<<(ostream &,Complex &);     //声明重载运算符“<<”
	friend istreamb&operator>>(istream &,Complex &);     //声明重载运算符“>>”
private:
	double real;
	double imag;
};

ostream &operator<<(ostream& output,Complex& c)          //定义重载运算符“<<”
{
	output<<"("<<c.real<<"+"<<c.imag<<"i)";
	return output;
}

istream &operator>>(istream &input,Complex &c)           //定义重载运算符“>>”
{
	cout<<″input real part and imaginary part of complex number:″;
	input>>c.real>>c.imag;
	return input;
}

int main()
{
	Complex c1,c2;
	cin>>c1>>c2;
	cout<<"c1="<<c1<<endl;
	cout<<"c2="<<c2<<endl;
	return 0;
}
```