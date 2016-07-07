---
layout: post
title: 引用
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

引用，又称别名，可以作为某一对象的另一个名字，是由C++引入的特性。通过引用可以间接地操纵对象，其使用方式类似于指针，但是不需要指针的语法。

##### 引用的基本问题

引用可以看作对象的一个别名，可以通过操作这个别名来操作实际对象。注意：引用在声明的同时必须被初始化。以下代码会出现编译错误：

```cpp
int a=1;
int &b;  //编译出错
```

应该称：

```cpp
int &b=a;//b为a的引用
```

##### 1.分析代码写结果--一般变量引用

```cpp
#include <iostream>
#include <string>

using namespace std;

int main(){
	int a=10;
	int b=20;
	int &rn=a;
	int equal;
	rn=b;                 //a=b
	cout<<"a="<<a<<endl;  //20
	cout<<"b="<<b<<endl;  //20

	rn=100;
	cout<<"a="<<a<<endl;  //100
	cout<<"b="<<b<<endl;  //20
	equal=(&a==&rn)?1:0;  //1
	cout<<"equal="<<equal<<endl;
	system("pause");
}
```

##### 2.分析代码写结果--指针变量引用

````cpp
#include <iostream>

using namespace std;

int main(){
	int a=1;
	int b=10;
	int* p=&a;
	int* &pa=p;    //pa为指针p的别名  p指向a
	(*pa)++;
	cout<<"a="<<a<<endl;     //2
	cout<<"b="<<b<<endl;     //10
	cout<<"*p="<<*p<<endl;   //2

	pa=&b;         //pa为指针p的别名  p指向b
	(*pa)++;
	cout<<"a="<<a<<endl;     //2
	cout<<"b="<<b<<endl;     //11
	cout<<"*p="<<*p<<endl;   //11

	system("pause");
}
```

##### 3.分析代码找错误--变量引用

```cpp
#include <iostream>

using namesapce std;

int main(){
	int a=1,b=2;
	int &c;      //错 定义引用要同时初始化
	int &d=a;    //对 d即是a
	&d=b;        //错 引用不能第二次赋值
	int *p;
	*p=5;        //错 p不知道指向那个空间
	return 0；
}
```

##### 参数引用

引用的一个重要作用就是**作为函数的参数**。**如果C语言中有大块数据作参数传递，采用的方案往往是指针。C++增加了一种同样有效率的选择，这就是引用**。


##### 4.交换两个字符串--参数引用

```cpp
#include <iostream>

using namespace std;

void swap(char *&x,char *&y){
	char *temp;
	temp=x;
	x=y;
	y=temp;
}

int main(){
	char *ap="hello";
	char *bp="how are you?";
	cout<<"ap:"<<ap<<endl;
	cout<<"bp:"<<bp<<endl;
	swap(ap,bp);
	cout<<"swap ap,bp"<<endl;
	cout<<"ap:"<<ap<<endl;
	cout<<"bp:"<<bp<<endl;

	system("pause");
}
```

运行结果：

![](/images/posts/Cpoint/58.png)

如果不使用引用，可以使用二维指针可以达到同样的效果

```cpp
void swap1(char **x,char **y){
    //x指针变量指向另一个指针变量*x 
    //*x为指针变量指向字符串 
	//**x为字符串
	char *temp;    //地址变量
	temp=*x;
	*x=*y;
	*y=temp;       
}

swap1(&ap,&bp);    //调用
```

##### 程序查错--参数引用

```cpp
#include <iostream>

using namespace std;

const float pi=3.14f;
float f;

float f1(float r){
	f=r*r*pi;
	return f;        
}

float& f2(float r){
	f=r*r*pi;
	return f;
}

int main(){
18	float f1(float=5);
19	float &f2(float=5);
20	float a=f1();
21	float& b=f1();
22	float c=f2();
23	float& d=f2();
24	d+=1.0f;
	cout<<"a="<<a<<endl;   //78.5
	cout<<"b="<<b<<endl;
	cout<<"c="<<c<<endl;   //78.5
	cout<<"d="<<c<<endl;   //79.5
	cout<<"f="<<f<<endl;   //79.5
	return 0;
}
```

第18行正确，声明函数f1()的默认参数调用，其默认参数值为5；

第19行正确，声明函数f2()的默认参数调用，其默认参数值为5；

第20行正确，将变量a赋值f1()的返回值；

第21行错误，将变量b赋值为f1()的返回值，**因为在f1()函数里，全局变量f的值78.5赋给临时变量temp，而temp变量由编译器隐式建立，然后再建立temp的引用b。这里对临时变量temp进行引用会发生错误**。

第22行正确，**f2()函数在返回值时并没有隐式地建立临时变量temp，而是直接地将全局变量f返回给主函数**。

第23行正确，**主函数中不定义变量，而是直接使用全局变量的引用，这种事最节省内存空间的方式。但必须注意它所引用变量的有效期，此处全局变量f的有效期肯定长于引用d，所以是安全的，否则，会出现错误**。

```cpp
#include <iostream>

using namespace std;

int i=10;

int &test(){
	return i;  
}

int main(){
	int &j=test();  //引用全局变量
	j++;
	cout<<i<<endl;  //11
	system("pause");
}
```

```cpp
#include <iostream>

using namespace std;

int &test(){
	int i=10;
	return i;  
}

int main(){
	int &j=test();  //引用局部变量 
	j++;
	cout<<j<<endl;  //11  没有报错
	system("pause");
}
```


##### 常量引用

常引用声明方式：const 类型标识符 &引用名=目标变量名。例如：

```cpp
int a;
const int &refa=a;
refa=1;   //错误
a=1;      //正确
```

因为**上述程序不能通过引用对目标变量的值进行修改，从而使引用的目标成为常量，达到引用安全目的**。


##### 程序查错--参数引用

```cpp
#include <iostream>
using namespace std;

class Test{
public:
	void f(const int &arg);
private:
	int value;
};

void Test::f(const int &arg){
	arg=10;          //出错  const变量不可修改
	cout<<"arg="<<arg<<endl;
	value=20;
}

int main(){
	int a=7;
	const int b=10;
	int &c=b;       //出错 因为b是常量，而c不是常量引用，所以c不可以是b的引用
	const int &d=a;
	a++;
	d++;            //出错 常引用不可修改
	Test test;
	test.f(a);
	cout<<"a="<<a<<endl;

}
```

##### 引用与指针的区别

**指针与引用编写方法完全不同（指针使用操作符*和->,引用使用操作符&），但是它们有类似的功能。指针与引用都可以间接引用其他对象**。

##### 7.指针和引用有什么区别？

1.初始化要求不同。**引用在创建的同时必须初始化，而指针在定义的时候不必初始化，可以在定义后面的任何地方重新赋值**。

2.可修改性不同。**引用一旦被初始化，它就不能被另一个对象引用；而指针在任何时候都可以指向另一个对象**。

3.不存在NULL引用。**引用不能使用指向空值的引用，它必须指向某个对象；而指针则可以是NULL，不需要总是指向某些对象，可以把指针指向任意对象，所以指针更加灵活，但也容易出错**。

4.测试时的区别。**由于引用不会指向空值，这意味着使用引用之前不需要测试它的合法性；而指针则需要经常进行测试。因此使用引用的代码效率比使用指针要高**。

5.应用的区别。**如果指向一个对象后就不会改变指向，那么应该使用引用。如果指向NULL（不指向任何对象）或在不同时刻指向不同的对象，应该使用指针**。

总的来说，**引用既具有指针效率，又具有变量使用的方便性和直观性**。

##### 8.为什么引用比指针安全？

由于不存在空引用，并且引用一旦被初始化指向一个对象，它就不能被改变为另一个对象的引用，因此引用很安全。指针可以随时指向别的对象，并且可以不被初始化，所以不安全。