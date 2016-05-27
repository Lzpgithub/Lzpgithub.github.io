---
layout: post
title: 常变量、常量指针和指针常量
categories: C和C++基础
description: 常变量、常量指针和指针常量
keywords: 常变量，常量指针和指针常量
---

#### 常变量(常量)

比如：

```cpp
const int n=20;    //声明时必须同时赋值初始化
int const n=20;
```

因为有了const修饰的n，我们不再把它称为变量，而称为常变量，简称常量，代表20这个数。**同时n不能在别的地方重新赋值。声明定义常变量时必须同时进行初始化**。同时，以上两行代码是完全等价的，因为主要是看const修饰谁。无论const放在int前面还是后面，最终修饰的都是n。


#### 常量指针(常指针)

常量指针就是我们说的指向常量的指针，顾名思义，它是用来指向常量的，意为常变量的指针，简称为常指针。

```cpp
#include <iostream>

using namespace std;

int main(){
	const int i=5;     //定义一个int型常变量
	const int *p;      //定义一个int型常指针
	p=&i;              //用常指针指向常变量
	cout<<*p<<endl;
	system("pause");
}
```

我们不能使用普通指针变量来指向常变量，必须用常量指针来指向常变量，原因也很容易理解，我们用普通指针指向常变量之后，有可能就会进行改变常变量数值的操作，这样做是不被允许的：

```cpp
#include <iostream>

using namespace std;

int main(){
	const int i=5;    //定义一个常变量
	int *p;           //定义了一个int型指针 
	p=&i;             //使用一般的指针指向常变量会报错
	cout<<*p<<endl;
	system("pause");
}
```

用常指针也是可以指向一般变量的，不过不可以通过常指针来更改一般变量的值，但是可以通过一般变量的变量名来修改变量的值。

```cpp
#include <iostream>

using namespace std;

int main(){
	int i=5;             //定义一个一般变量
	const int *p;        //定义了一个常指针 
	p=&i;                //可使用常指针指向一般变量 
	cout<<*p<<endl;
	i=10;                //可通过一般变量的变量名来修改值
	cout<<*p<<endl; 
	*p=12;               //通过常指针来更改一般变量的值会报错
	int j=15;
	p=&j;                //可以在任意时候重新赋值一个新的内存地址
	cout<<*p<<endl; 
	system("pause");
}
```

首先const修饰的是整个\*p，所以\*p是常量，是不能被赋值的，其次p的前面并没有用const修饰，因此p是一个普通的指针变量(常指针可被认为一般指针变量)，能被赋值重新指向另一个内存地址。

```cpp
const int *p;
int const *p;
```

以上两行代码完全等价，都是声明定义常指针p，因为const都是修饰\*p。

小结：常指针可以指向常变量和一般变量，但是都不能通过指针改变它指向的变量的值。同时不用普通指针变量指向常变量。


#### 指针常量

**指针常量是指的指针本身就是一个常变量，一经赋初值就不可以被更改**。意为指针的常变量，上面已经说过，**只有常指针才可以指向常变量**，所以指针常量只可以指向一般变量，而且一旦赋给了初值，就不可以再更改。

定义方法：

```cpp
类型名 * const 指针常量名=变量地址;
```

因为指针是常变量所以它在定义的时候就应该赋初值。

```cpp
#include <iostream>

using namespace std;

int main(){
	int i=5;             //定义一个一般变量
	int * const p=&i;    //定义了一个指针常量 必须同时赋值初始化
	cout<<*p<<endl; 
	//通过指针来改变指向的变量的值 其实指向的地址空间不变 空间的内容却变了
	*p=30;               
	cout<<*p<<endl;
	system("pause");
}
```

因为p有了const的修饰，所以p是一个指针常量，也就是说p值是不可修改的，因为整个\*p的前面没有const的修饰，因此\*p是变量而不是常量，我们可以通过\*p来修改它所指向的内存i的值。

特殊情形：

```cpp
#include <iostream>

using namespace std;

int main(){
	int n=5;
	const int * const p=&n;              
	cout<<*p<<endl;
	n=10;
	cout<<*p<<endl;
	system("pause");
}
```

p的值不能改，也不能通过\*p修改n的值，因为无论是p还是\*p，都被const修饰了。但是n的值可以改，因为n就是普通变量。

