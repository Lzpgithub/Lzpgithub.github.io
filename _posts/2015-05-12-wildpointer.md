---
layout: post
title: 野指针
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

野指针是指程序员或操作者不能控制的指针。**当程序里定义了一个指针而又没有给这个指针一个具体地址指向时，这个指针会随意地指向一个地址，这样的指针就是一个野指针**。

如果这个地址后面的内存空间没有什么重要的数据则不会造成不严重的后果，但是一旦里面存放了有用的数据，那么这些数据随时都有被野指针存取的危险，如果是这样，数据就会别破坏，程序也会崩溃。因此在程序里禁止野指针的存在。

##### 什么是野指针？

**野指针不是NULL指针，是指向“垃圾”内存的指针**。人们一般不会错用NULL指针，但是“野指针”是很难判断出，是很危险的，而且if语句对它不起作用。“野指针”的成因主要有两种。

(1).指针变量没有初始化。任何指针变量刚被创建时不会自动成为NULL指针，它的默认值是随机的，所以，指针变量在创建的同时应当初始化，要么将指针设置为NULL，要么指向合法内存。

(2).指针p被free或者delete之后，没有置为NULL。


##### 分析代码查错--“野指针”的危害

下面的程序片段有什么重大的bug？

```cpp
short *bufptr;
short bufarray[20];
short var=0x20;
*bufptr=var;          //错 指针bufptr没有指向特定内存地址
bufarray[0]=var;
```

可以对bufptr进行初始化：

```cpp
short *bufptr=(short*)malloc(sizeof(short));
```

##### 30.有了malloc/free，为什么还要new/delete

malloc与free都是C++/C语言的标准库函数，new/delete是C++的运算符。它们都可以用于申请动态内存和释放内存。

**对于非内部数据类型的对象而言**，仅用malloc/free无法满足动态对象的要求。对象在创建的同时要自动执行构造函数，对象在消亡之前要自动执行析构函数。由于malloc/free是库函数而不是运算符，不在编译器控制权限之内，因此不能把执行构造函数和析构函数的任务强加于malloc/free函数。

简单地理解，**malloc函数只能申请内存，不能在申请内存的时候同时对所申请的内存进行初始化工作。malloc函数只能得到指针，类的构造函数不能使用指针直接调用，malloc无法做到自动调用构造函数。因此C++需要一个能完成动态内存分配和初始化工作的运算符new，以及一个能完成清理与释放内存工作的运算符delete，自动地调用构造函数和析构函数**。

```cpp
#include <iostream>

using namespace std;

class Obj{
public:
	Obj(void){        //构造函数
		cout<<"Initialization"<<endl;
	}

	~Obj(void){       //析构函数
		cout<<"Destroy"<<endl;
	}
};

void UseMallocFree(void){
	cout<<"in UseMallocFree()..."<<endl;
	Obj *a=(Obj*)malloc(sizeof(Obj));
	free(a);
}

void UseNewDelete(void){
	cout<<"in UseNewDelete()..."<<endl;
	Obj *a=new Obj;
	delete a;
}

int main(){
	UseMallocFree();
	UseNewDelete();
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/62.png)

可以看出函数UseMallocFree()执行时，类Obj的构造函数和析构函数都不会被调用，而函数UseNewDelete()执行时，类Obj的构造函数和析构函数会被调用。

**由于内部数据类型的“对象”没有构造与析构的过程，对它们而言malloc/free和new/delete是等价的**。

既然new/delete的功能完全覆盖了malloc/free，为什么C++不把malloc/free淘汰出局呢？**这是因为C++程序经常要调用C函数，而C程序只能用malloc/free管理动态内存**。

```cpp
#include <iostream>
#include <string>

using namespace std;

struct A{
	string str;
	int data;
};

int main(){
	A *a = (A*)malloc(sizeof(A));
	a->str = "testdata";           //报错
	cout<<a->str<<endl;

	system("pause");
}
```

原因在于调用malloc函数时并没有调用string的构造函数，导致str未初始化。要避免这样的问题，用 C 的方式可以使用字符数组（char *str），或者在 C＋＋里这样使用：

```cpp
#include <iostream>
#include <string>

using namespace std;

struct A{
	string str;
	int data;
};

int main(){
	A *a = new A;
	a->str = "testdata";    
	cout<<a->str<<endl;
	system("pause");
}
```

##### 31.程序改错--指针的初始化

```cpp
#include <stdio.h>
#include <malloc.h>

struct Tag_Node{
	struct Tag_Node* left;
	struct Tag_Node* right;
	int value;
};

typedef struct Tag_Node TNode;

TNode* root=NULL;

void append(int N);

int main(){
	append(63);
	append(45);
	append(32);
	append(77);
	append(96);
	append(21);
	append(17);
	print();            //打印
}

void append(int N){
	TNode* NewNode=(TNode*)malloc(sizeof(TNode));
	NewNode->value=N;
	if(root==NULL){      //如果没有根结点 创建根结点
		root=NewNode;
		return;
	}else{
		TNode* temp;
		temp=root;       //存储根结点
		while((N>=temp->value&&temp->left!=NULL)||
			(N<temp->value&&temp->right!=NULL)){
			while(N>=temp->value&&temp->left!=NULL)
				temp=temp->left;
			while(N<temp->value&&temp->right!=NULL)
				temp=temp->right;
		}
		if(N>=temp->value)
			temp->left=NewNode;
		else
			temp->right=NewNode;
		return;
	}
}
```

temp指针left或指针right要与NULL进行判断，然而对堆中分配的内存只做成员value的初始化，没有把left和right初始化为NULL，因此指针left和指针right与NULL进行判断不起作用。从而程序会对野指针指向的地址进行赋值，从而导致程序崩溃。

改正后的代码：

```cpp
#include <stdio.h>
#include <malloc.h>

struct Tag_Node{
	struct Tag_Node* left;
	struct Tag_Node* right;
	int value;
};

typedef struct Tag_Node TNode;

TNode* root=NULL;

void append(int N);
void print();

int main(){
	append(63);
	append(45);
	append(32);
	append(77);
	append(96);
	append(21);
	append(17);
	printf("head:%d\n",root->value);
	print()；               //打印
}

void append(int N){
	TNode* NewNode=(TNode*)malloc(sizeof(TNode));
	NewNode->value=N;
	NewNode->left=NULL;  //初始化left
	NewNode->right=NULL; //初始化right
	if(root==NULL){      //如果没有根结点 创建根结点
		root=NewNode;
		return;
	}else{
		TNode* temp;
		temp=root;       //存储根结点
		while((N>=temp->value&&temp->left!=NULL)||
			(N<temp->value&&temp->right!=NULL)){
			while(N>=temp->value&&temp->left!=NULL)
				temp=temp->left;
			while(N<temp->value&&temp->right!=NULL)
				temp=temp->right;
		}
		if(N>=temp->value){
			temp->left=NewNode;
			NewNode->right=temp;  //形成双向链表
		}else{
			temp->right=NewNode;
			NewNode->left=temp;   //形成双向链表
		}
		return;
	}
}

void print(){
	TNode* leftside=NULL;
	if(root==NULL){
		printf("There is not any element");
		return;
	}
	leftside=root->left;
	while(1){
		if(leftside->left==NULL){
			break;
		}
		leftside=leftside->left;
	}
	while(leftside!=NULL){
		printf("%d",leftside->value);
		leftside=leftside->right;
	}
}
```

该程序其实完成的就是堆，大的放在左边，小的放在右边。