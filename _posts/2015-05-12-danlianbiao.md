---
layout: post
title: 单链表
categories: 数据结构
description: 单链表
keywords: C, 单链表
---

##### 线性链表

线性表的链式存储结构的特点是用一组任意的存储单元存储线性表的数据元素，存储单元可以是连续的，也可以是不连续的。为了表示每个数据元素与其直接后继数据元素之间的逻辑关系，对每个数据来说，除了存储其本身的信息之外，还需要存储一个指示其直接后继的信息，即直接后继的存储位置，这两部分信息组成数据元素的存储映像，称之为结点。它包括两个域：其中存储数据元素信息组成的域称之为数据域，存储直接后继存储位置的域称为指针域。

![](/images/posts/Datastructure/6.png)

![](/images/posts/Datastructure/7.png)


线性链表的单链表的存储结构：

```cpp
typedef struct LNode{
	ElemType data;
	struct LNode *next;
}LNode,*LinkList;
```

我们在单链表中的第一个结点之前附设一个结点，称之为头结点，头结点的数据域可以不存储任何信息，也可以存储如线性表的长度等附加信息，头结点的指针域存储指向第一个结点的指针，即第一个元素结点的存储位置，称之为头指针。

##### 单链表的插入

设插入数据为e，首先要生成一个数据域为e的结点s，先确定插入位置，然后插入单链表中,时间复杂度为O(n)。

![](/images/posts/Datastructure/8.png)

```cpp
Status ListInsert_L(LinkList &L,int i,ElemType e){
	LinkList p;
	p=L;
	int j=0;
	while(p&&j<i-1){    //寻找第i-1个节点
		p=p->next;
		j++;
	}
	if(!p||j>i-1) return ERROR;  //i小于1或者大于表长加1
	LinkList s=(LinkList)malloc(sizeof(LNode));
	s->data=e;
	s->next=p->next;
	p->next=s;
	return OK;
}
```

##### 单链表的删除

先确定删除位置，然后删除结点，时间复杂度为O(n)。

![](/images/posts/Datastructure/9.png)

```cpp
Status ListDelete_L(LinkList &L,int i,ElemType &e){
	LinkList p=L;
	int j=0;
	while(p->next&&j<i-1){       //寻找第i个节点
		p=p->next;
		j++;
	}
	if(!(p->next)||j>i-1) return ERROR;   //删除位置不合理
	LinkList q=p->next;
	p->next=q->next;
	e=q->data;
	free(q);
	return OK;
}
```

##### 完整代码（C代码）

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

typedef struct LNode{
	ElemType data;
	struct LNode *next;
}LNode,*LinkList;


//创建单链表
void CreateList_L(LinkList &L,int n){
	L=(LinkList)malloc(sizeof(LNode));   
	LinkList q;
	L->next=NULL;         //建立一个带头节点的单链表
	for(int i=n;i>0;i--){
		LinkList p;
		p=(LinkList)malloc(sizeof(LNode));
		cin>>p->data;

		//p->next=L->next;  //插入到表头
		//L->next=p;

		if(i==n){           //插入到表尾
			L->next=p;
			p->next=NULL;
			q=p;
		}else{
			p->next=NULL;
			q->next=p;
			q=p;
		}

	}
}

//给线性表插入元素
Status ListInsert_L(LinkList &L,int i,ElemType e){
	LinkList p;
	p=L;
	int j=0;
	while(p&&j<i-1){    //寻找第i-1个节点
		p=p->next;
		j++;
	}
	if(!p||j>i-1) return ERROR;  //i小于1或者大于表长加1
	LinkList s=(LinkList)malloc(sizeof(LNode));
	s->data=e;
	s->next=p->next;
	p->next=s;
	return OK;
}

//删除链表中的元素
Status ListDelete_L(LinkList &L,int i,ElemType &e){
	LinkList p=L;
	int j=0;
	while(p->next&&j<i-1){       //寻找第i个节点
		p=p->next;
		j++;
	}
	if(!(p->next)||j>i-1) return ERROR;   //删除位置不合理
	LinkList q=p->next;
	p->next=q->next;
	e=q->data;
	free(q);
	return OK;
}

//打印链表
void PrintLink(LinkList L){
	LinkList p;
	p=L->next;
	while(p->next!=NULL){
		cout<<p->data<<" ";
		p=p->next;
	}
	cout<<p->data<<endl;
}

int main(){
	LinkList L;
	CreateList_L(L,5);
	PrintLink(L);
	ListInsert_L(L,2,66);
	PrintLink(L);
	int e;
	ListDelete_L(L,4,e);
	PrintLink(L);
	system("pause");
}
```

##### 完整代码（C++代码）

```cpp
#include <iostream>
using namespace std;

struct ListNode{                         //单链表结构体
	int val;
	struct ListNode *next;
	ListNode(int v):val(v){
		next=NULL;
	}
};

ListNode *creatList(){                    //创建链表
	int N;
	cin>>N;
	ListNode *L;
	ListNode *pre;
	for(int i=0;i<N;i++){
		int num;
		cin>>num;
		if(i==0){
			L=new ListNode(num);
			pre=L;
		}else{
			ListNode *temp=new ListNode(num);
			pre->next=temp;
			pre=temp;
		}
	}
	return L;
}


void printList(ListNode *L){            //打印链表
	ListNode *p=L;
	while(p!=NULL){
		cout<<p->val<<" ";
		p=p->next;
	}
	cout<<endl;
}


void InsertList(ListNode *L,int i,int e){     //插入结点
	ListNode *p=L;
	int j=1;
	while(p!=NULL&&j<i-1){
		j++;
		p=p->next;
	}
	ListNode *q=new ListNode(e);
	q->next=p->next;
	p->next=q;
}

void DeleteList(ListNode *L,int i){
	ListNode *p=L;
	int j=1;
	while(p!=NULL&&j<i-1){
		j++;
		p=p->next;
	}
	ListNode *q=p->next;
	p->next=q->next;
	delete q;
}

int main(){
	ListNode *L;
	L=creatList();
	printList(L);
	InsertList(L,2,10);
	printList(L);
	DeleteList(L,4);
	printList(L);
	system("pause");
}
```