---
layout: post
title: 棋子翻转(美团)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

在4x4的棋盘上摆满了黑白棋子，黑白两色的位置和数目随机，其中左上角坐标为(1,1),右下角坐标为(4,4),现在依次有一些翻转操作，要对一些给定支点坐标为中心的上下左右四个棋子的颜色进行翻转，请计算出翻转后的棋盘颜色。

给定两个数组A和f,分别为初始棋盘和翻转位置。其中翻转位置共有3个。请返回翻转后的棋盘。

测试样例：

[[0,0,1,1],[1,0,1,0],[0,1,1,0],[0,0,1,0]],[[2,2],[3,3],[4,4]]

返回：[[0,1,1,1],[0,0,1,0],[0,1,1,0],[0,0,1,0]]

#### 程序代码



```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<vector<int> > flipChess(vector<vector<int> > A, vector<vector<int> > f){
	for(int i=0;i<f.size();i++){
		int x=f[i][0]-1;
		int y=f[i][1]-1;
		if(x-1>=0){
			if(A[x-1][y]==1)
				A[x-1][y]=0;
			else
				A[x-1][y]=1;
		}
		if(x+1<=3){
			if(A[x+1][y]==1)
				A[x+1][y]=0;
			else
				A[x+1][y]=1;
		}

		if(y-1>=0){
			if(A[x][y-1]==1)
				A[x][y-1]=0;
			else
				A[x][y-1]=1;
		}
		if(y+1<=3){
			if(A[x][y+1]==1)
				A[x][y+1]=0;
			else
				A[x][y+1]=1;
		}	
	}

	for(int i=0;i<A.size();i++){
		for(int j=0;j<A[i].size();j++){
			cout<<A[i][j]<<" ";
		}
		cout<<endl;
	}
	return A;
}

int main(){
	int a1[4]={0,0,1,1};
	int a2[4]={1,0,1,0};
	int a3[4]={0,1,1,0};
	int a4[4]={0,0,1,0};
	vector<int> arr1(a1,a1+4);
	vector<int> arr2(a2,a2+4);
	vector<int> arr3(a3,a3+4);
	vector<int> arr4(a4,a4+4);
	vector<vector<int> >A;
	A.push_back(arr1);
	A.push_back(arr2);
	A.push_back(arr3);
	A.push_back(arr4);
	int af1[2]={2,2};
	int af2[2]={3,3};
	int af3[2]={4,4};
	vector<int> f1(af1,af1+2);
	vector<int> f2(af2,af2+2);
	vector<int> f3(af3,af3+2);
	vector<vector<int> > f;
	f.push_back(f1);
	f.push_back(f2);
	f.push_back(f3);

	flipChess(A,f);

	system("pause");
}
```

