---
layout: post
title: 网易2016实习研发工程师编程题
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 比较重量

小明陪小红去看钻石，他们从一堆钻石中随机抽取两颗并比较她们的重量。这些钻石的重量各不相同。在他们们比较了一段时间后，它们看中了两颗钻石g1和g2。现在请你根据之前比较的信息判断这两颗钻石的哪颗更重。

给定两颗钻石的编号g1,g2，编号从1开始，同时给定关系数组vector,其中元素为一些二元组，第一个元素为一次比较中较重的钻石的编号，第二个元素为较轻的钻石的编号。最后给定之前的比较次数n。请返回这两颗钻石的关系，若g1更重返回1，g2更重返回-1，无法判断返回0。输入数据保证合法，不会有矛盾情况出现。

##### 测试样例：

2,3,[[1,2],[2,4],[1,3],[4,3]],4

返回: 1

解析：就是一个森林,关系存在就是以g2为根节点的树下面的节点中有g1,或者以g1为根节点的树的下面的节点包含g2，我们采取**层序遍历**的方式遍历以g1开头的整棵树,和以g2开头的整棵树。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int cmp(int g1,int g2,vector<vector<int> > records,int n){
	int *m=(int*)malloc(n*sizeof(int));
	vector<int> stack;

	for(int i=0;i<n;i++)
		m[i]=0;
	stack.push_back(g1);
	while(!stack.empty()){
		int b=stack.back();
		stack.pop_back();
		for(int i=0;i<n;i++){
			if(records[i][0]==b&&m[i]==false){
				if(records[i][1]==g2){
					return 1;
				}
				stack.push_back(records[i][1]);
				m[i]=1;
			}
		}
	}

	for(int i=0;i<n;i++)
		m[i]=0;
	stack.push_back(g2);
	while(!stack.empty()){
		int b=stack.back();
		stack.pop_back();
		for(int i=0;i<n;i++){
			if(records[i][0]==b&&m[i]==false){
				if(records[i][1]==g1){
					return -1;
				}
				stack.push_back(records[i][1]);
				m[i]=1;
			}
		}
	}

	return 0;
}

int main(){
	int g1=2;
	int g2=3;
	int n=4;
	vector<vector<int> > records;
	vector<int> r1;
	r1.push_back(1);
	r1.push_back(2);
	records.push_back(r1);

	vector<int> r2;
	r2.push_back(2);
	r2.push_back(4);
	records.push_back(r2);

	vector<int> r3;
	r3.push_back(1);
	r3.push_back(3);
	records.push_back(r3);

	vector<int> r4;
	r4.push_back(4);
	r4.push_back(3);
	records.push_back(r4);

	cout<<cmp(g1,g2,records,n)<<endl;

	system("pause");
}
```

#### 二叉树

有一棵二叉树，树上每个点标有权值，权值各不相同，请设计一个算法算出权值最大的叶节点到权值最小的叶节点的距离。二叉树每条边的距离为1，一个节点经过多少条边到达另一个节点为这两个节点之间的距离。

给定二叉树的根节点root，请返回所求距离。

```cpp
#include <iostream>
#include <list>
#include <math.h>

using namespace std;

struct TreeNode{
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x):val(x),left(NULL),right(NULL){}
};

void preorderVist(TreeNode *T){
	if(T!=NULL){
		cout<<T->val<<endl;
		preorderVist(T->left);
		preorderVist(T->right);
	}
}

int getDis(TreeNode* root){
	list<TreeNode*> tree;
	int max=1;
	int min=1;
	int maxnum=root->val;
	int minnum=root->val;
	int count=1;
	if(root!=NULL){
		tree.push_back(root);
		int pos=1;                       //记录每个结点的标号
		while(!tree.empty()){            //层序变量所有点 确定最大值点和最小值点的编号
			for(int i=0;i<count;i++){   
				TreeNode *temp=tree.front();
				tree.pop_front();

				pos++;
				//cout<<pos<<endl;
				if(temp->left!=NULL){
					tree.push_back(temp->left);
					if(maxnum<temp->left->val){
						maxnum=temp->left->val;
						max=pos;
					}
					if(minnum>temp->left->val){
						minnum=temp->left->val;
						min=pos;
					}
				}

				pos++;
				//cout<<pos<<endl;
				if(temp->right!=NULL){
					tree.push_back(temp->right);
					if(maxnum<temp->right->val){
						maxnum=temp->right->val;
						max=pos;
					}
					if(minnum>temp->right->val){
						minnum=temp->right->val;
						min=pos;
					}
				}
			}
			count=tree.size();
		}

		int maxd=log(max*1.0)/log(2.0);       //计算两个所在的层数
		int mind=log(min*1.0)/log(2.0);
		
		int path=0;                           //根据编号来确定路径
		if(maxd!=mind){                       //若二者的层数不再同一层
			if(maxd>mind){
				path=maxd-mind;
				max=max/pow(2.0,path);
			}else{
				path=mind-maxd;
				min=min/pow(2.0,path);
			}
			while(max!=min){
				max=max/2;
				min=min/2;
				path=path+2;
			}
		}else{                               //若二者的层数相同
			while(max!=min){
				max=max/2;
				min=min/2;
				path=path+2;
			}
		}
		return path;
	}
	return 0;
}

int main(){
	TreeNode *root=new TreeNode(10);
	TreeNode *node2=new TreeNode(5);
	TreeNode *node3=new TreeNode(6);
	TreeNode *node4=new TreeNode(7);
	TreeNode *node5=new TreeNode(2);
	TreeNode *node6=new TreeNode(8);
	root->left=node2;
	root->right=node3;
	node2->left=node4;
	node2->right=node5;
	node3->left=node6;
	getDis(root);
	system("pause");
}
```