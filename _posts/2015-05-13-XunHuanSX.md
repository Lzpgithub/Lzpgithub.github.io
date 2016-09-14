---
layout: post
title: 循环链表和双向链表
categories: 数据结构
description: 循环链表和双向链表
keywords: C, 循环链表, 双向链表
---

#### 循环链表

循环链表是另一种形式的链式存储结构，它的特点是表中最后的一个结点的指针域指向头结点，整个链表形成一个环。由此，从表中任一结点出发，均可找到表中的其他结点。

![](/images/posts/Datastructure/10.png)

循环链表的操作和线性单链表基本一致，差别仅在于算法中循环条件不是p或p->next是否为空，而是它们是否等于头指针。但有时候，若在循环链表中设立尾指针而不设头指针，可使某些操作简化。例如将两个线性表合并成一个表时，仅需将一个表的表尾和另一个表的表头相接，运算时间为O(1)。

![](/images/posts/Datastructure/11.png)


#### 双向链表

双向链表的结点中有两个指针域，其一指向直接后继，另一指向直接前驱，存储结构为：

```cpp
typedef struct DuLNode{
	ElemType data;
	struct DuLNode *prior;
	struct DuLNode *next;
}DuLNode,*DuLinkList;
```

##### 双向链表的插入与删除

时间复杂度均为O(n)。

![](/images/posts/Datastructure/12.png)

```cpp
Status ListInsert_DuL(DuLinkList &L,int i,ElemType e){
	DuLinkList p,s;
	int j=0;
	p=L->next;
	while(p!=L&&j<i-1){
		p=p->next;
		j++;
	}
	if(!(s=(DuLinkList)malloc(sizeof(DuLNode)))) return ERROR;
	s->data=e;
	s->prior=p->prior;
	p->prior->next=s;
	s->next=p;
	p->prior=s;
	return OK;
}
```

![](/images/posts/Datastructure/13.png)

```cpp
Status ListDelete_DuL(DuLinkList &L,int i,ElemType &e){
	DuLinkList p,s;
	int j=0;
	p=L->next;
	while(p!=L&&j<i-1){
		p=p->next;
		j++;
	}
	e=p->data;
	p->prior->next=p->next;
	p->next->prior=p->prior;
	free(p);
	return OK;
}
```

双向链表完整代码（C语言）：

```cpp
#include <iostream>
#include <stdio.h>
#include <stdlib.h>

//函数结果状态代码
#define OK           1
#define ERROR        0

using namespace std;

typedef int ElemType;     //定义数据类型
typedef int Status;  

//双向表的存储结构
typedef struct DuLNode{
	ElemType data;
	struct DuLNode *prior;
	struct DuLNode *next;
}DuLNode,*DuLinkList;

//创建双向表
void CreateList(DuLinkList &L,int n){
	L=(DuLinkList)malloc(sizeof(DuLNode));
	DuLinkList q;
	for(int i=0;i<n;i++){
		DuLinkList s=(DuLinkList)malloc(sizeof(DuLNode));
		cin>>s->data;
		if(i==0){
			L->next=s;
			L->prior=s;
			s->prior=L;
			s->next=L;
			q=s;
		}else{
			s->next=L;
			L->prior=s;
			s->prior=q;
			q->next=s;
			q=s;
		}
	}
}

//向前打印
void printDuLinkNodePrior(DuLinkList L){
	DuLinkList p;
	p=L->prior;
	while(p!=L){
		cout<<p->data<<" ";
		p=p->prior;
	}
	cout<<endl;
}

//向后打印
void printDuLinkNodeNext(DuLinkList L){
	DuLinkList p;
	p=L->next;
	while(p!=L){
		cout<<p->data<<" ";
		p=p->next;
	}
	cout<<endl;
}

//插入元素
Status ListInsert_DuL(DuLinkList &L,int i,ElemType e){
	DuLinkList p,s;
	int j=0;
	p=L->next;
	while(p!=L&&j<i-1){
		p=p->next;
		j++;
	}
	if(!(s=(DuLinkList)malloc(sizeof(DuLNode)))) return ERROR;
	s->data=e;
	s->prior=p->prior;
	p->prior->next=s;
	s->next=p;
	p->prior=s;
	return OK;
}


//删除元素
Status ListDelete_DuL(DuLinkList &L,int i,ElemType &e){
	DuLinkList p,s;
	int j=0;
	p=L->next;
	while(p!=L&&j<i-1){
		p=p->next;
		j++;
	}
	e=p->data;
	p->prior->next=p->next;
	p->next->prior=p->prior;
	free(p);
	return OK;
}

int main(){
	DuLinkList L;
	CreateList(L,5);
	printDuLinkNodeNext(L);
	ListInsert_DuL(L,3,86);
	printDuLinkNodeNext(L);
	int e;
	ListDelete_DuL(L,4,e);
	printDuLinkNodeNext(L);

	system("pause");
}
```

双向链表完整代码（C++语言）：

```cpp
#include <iostream>
using namespace std;

struct DulNode{                    //双向链表
	int val;
	struct DulNode *prior;
	struct DulNode *next;
	DulNode(int n):val(n){
		prior=NULL;
		next=NULL;
	}
};

DulNode *createDulList(){          //创建双向链表
	DulNode *L,*pre,*temp;
	int N;
	cin>>N;
	for(int i=0;i<N;i++){
		int num;
		cin>>num;
		if(i==0){
			L=new DulNode(num);
			pre=L;
		}else{
			temp=new DulNode(num);
			pre->next=temp;
			temp->prior=pre;
			pre=temp;
		}
	}
	L->prior=temp;
	temp->next=L;
	return L;
}

void printDulN(DulNode *L){      //向后打印
	DulNode *p=L;
	cout<<p->val<<" ";
	p=p->next;
	while(p!=L){
		cout<<p->val<<" ";
		p=p->next;
	}
	cout<<endl;
}

void printDulP(DulNode *L){      //向后打印
	DulNode *p=L->prior;
	cout<<p->val<<" ";
	p=p->prior;
	while(p!=L->prior){
		cout<<p->val<<" ";
		p=p->prior;
	}
	cout<<endl;
}

void InsertDulList(DulNode *L,int i,int e){    //插入操作
	DulNode *p=L;
	int j=1;
	while(j<i){
		j++;
		p=p->next;
	}
	DulNode *s=new DulNode(e);
	s->next=p;
	s->prior=p->prior;
	p->prior->next=s;
	p->prior=s;
}

void DeleteDulList(DulNode *L,int i){
	DulNode *p=L;
	int j=1;
	while(j<i){
		j++;
		p=p->next;
	}
	p->prior->next=p->next;
	p->next->prior=p->prior;
	delete p;
}

int main(){
	DulNode *L=createDulList();
	printDulN(L);
	printDulP(L);
	InsertDulList(L,2,12);
	printDulN(L);
	printDulP(L);
	DeleteDulList(L,4);
	printDulN(L);
	printDulP(L);

	system("pause");
}
```