---
layout: post
title: 动态内存的传递
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

C/C++编程时，会多次与内存管理打交道，因此掌握内存分配、传递、赋值与释放等知识十分必要。

##### 32.各种内存分配和释放的函数的联系和区别

解析：

C语言的标准内存分配函数有malloc、calloc、realloc、free等。

malloc与calloc的区别如下：

malloc调用形式为：

**(类型\*)malloc(size)**

在内存的动态存储区中分配长度为“size”字节的连续区域，返回该区域的首地址，此时内存中的值没有初始化，是一个随机数。

calloc调用形式为：

**(类型\*)calloc(n,size)**

在内存的动态存储区中分配n块长度为“size”字节的连续区域，返回首地址，此时内存中的值都被初始化为0。

realloc调用形式为：

**(类型\*)realloc(\*ptr,size)**

将ptr内存大小增大到“size”，增加的内存块没有初始化。

free的调用形式为**free(void \*ptr)**：释放ptr所指向的内存空间。

C++中用new和delete函数，调用类的构造函数和析构函数。

##### 33.程序找错--动态内存的传递

```cpp
#include <iostream>
using namespace std;

class Base{
private:
	char* name;
public:
	Base(char* className){
		name=new char[strlen(className)];   //sizeof
		strcpy(name,className);
	}	
	~Base(){
		delete name;
	}
	char* copyName(){
		char newname[10]="";
		strcpy(newname,name);
		return newname;
	}
	char* getName(){
		return name;
	}
};

class Subclass:public Base{
public:
	Subclass(char* className):Base(className){}
}；

int main(){
	Base *pBase=new Subclass("test");
	printf("name:%s\n",pBase->getName());
	printf("new name:%s\n",pBase->copyName());
	return 0;
}
```

代码：

```cpp
name=new char[strlen(className)+1];
```

改为：

```cpp
char *newname=new char[strlen(name)+1];
```


##### 程序分析改错--动态内存的传递

```cpp
#include <iostream>

using namespace std;

void GetMemory(char *p,int num){
	p=(char*)malloc(sizeof(char)*num);
}

int main(){
	char *str=NULL;
	GetMemory(str,10);
	strcpy(str,"hello");
	cout<<str<<endl;
	system("pause");
}
```

解析：

GetMemory()函数存有错误。因为编译器总是为函数的每个参数制作临时的变量。所以GetMemory()函数体内的p是main函数中str变量在GetMemory()函数中的一个备份。虽然代码p申请了堆内存，但是返回到main()函数时，str还是NULL，而不指向对内存，因此在调用strcpy时会导致程序崩溃。

同时，GetMemory()申请堆内存没有释放。

采用以下3种方法来解决动态内存不能传递的问题。

1.在C中，采用指向指针的指针解决这个问题，可以把str的地址传给GetMemory()函数。

2.在C++中，采用传递str指针的引用。

3.使用函数返回值来传递动态内存。

如：

```cpp
#include <iostream>

using namespace std;

void GetMemory(char *p,int num){
	p=(char*)malloc(sizeof(char)*num);
}

void GetMemory2(char **p,int num){        //指针的指针
	*p=(char*)malloc(sizeof(char)*num);
}

void GetMemory3(char *&p,int num){        //引用
	p=(char*)malloc(sizeof(char)*num);
}
 
char* GetMemory4(int num){                //返回指针
	char *p=(char*)malloc(sizeof(char)*num);
	return p;
}

int main(){
	char *str1=NULL;
	char *str2=NULL;
	char *str3=NULL;
	char *str4=NULL;

	GetMemory2(&str2,20);
	GetMemory3(str3,20);
	str4=GetMemory4(20);

	strcpy(str2,"GetMemory2");
	strcpy(str3,"GetMemory3");
	strcpy(str4,"GetMemory4");

	cout<<"str1==NULL?"<<(str1==NULL?"yes":"no")<<endl;
	cout<<"str2:"<<str2<<endl;
	cout<<"str3:"<<str3<<endl;
	cout<<"str4:"<<str4<<endl;

	free(str2);
	free(str3);
	free(str4);
	str2=NULL;       //防止野指针出现
	str3=NULL;
	str4=NULL;     
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/63.png)


##### 35.比较分析两个代码段的输出--动态内存传递

程序1：

```cpp
char* GetMemory(){
	char p[]="hello world";  //局部变量
	return p;
}

void Test(void){
	char *str=NULL;
	str=GetMemory();
	printf(str);             //输出乱码
}
```

程序2：

```cpp
void GetMemory(char *p){
	p=(char*)malloc(100);
}

void Test(void){
	char *str=NULL;
	GetMemory(str);
	strcpy(str,"hello world");  //出错
	printf(str);
}
```


##### 36.程序查错--“野指针”用于变量值的互换

```cpp
void swap(int *p1,int *p2){
	int *p;             //没有申请指向空间 运行会导致程序崩溃
	*p=*p1;
	*p1=*p2;
	*p2=*p;
}
```

应该改为：

```cpp
void swap(int *p1,int *p2){
	int p;
	p=*p1;
	*p1=*p2;
	*p2=p;
}
```

##### 全局变量和局部变量重名

```cpp
#include <iostream>

using namespace std;

int i=2;

int main(){
	int a=i;
	int i=3;;
	cout<<a<<endl;   //2
	cout<<i<<endl;   //3
	system("pause");
}
```

##### 37.内存的分配方式有几种

解析：

1.从静态存储区域分配内存。程序编译的时候内存已经分配好了，并且在程序的整个运行期间都存在，例如全局变量。

2.在栈上创建。在执行函数时，函数内部局部变量的存储单元可以在栈上创建，函数结束时这些存储单元自动被释放。处理器的指定集中有关于栈内存的分配运算，因此效率很高，但是分配的内存容量有限。

3.从堆上分配内存，亦称为动态内存分配。程序在运行的时候用malloc函数或new运算符申请任意大小的内存，程序员要用free函数或delete运算符释放内存。动态内存使用非常灵活，但问题也很多。