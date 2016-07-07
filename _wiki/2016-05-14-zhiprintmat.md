---
layout: post
title: 之字形打印矩阵
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

对于一个矩阵，请设计一个算法，将元素按“之”字形打印。具体见样例。
给定一个整数矩阵mat，以及他的维数nxm，请返回一个数组，其中元素依次为打印的数字。

测试样例：

[[1,2,3],[4,5,6],[7,8,9],[10,11,12]],4,3

返回：[1,2,3,6,5,4,7,8,9,12,11,10]


#### 程序代码

```cpp
#include <iostream>
#include <vector>
#include <math.h>

using namespace std;

vector<int> printMatrix(vector<vector<int> > mat,int n,int m){
	vector<int> r;
	for(int i=0;i<n;i++){
		int t1=pow(-1.0,i);
		int t2=((m-1)-t1*(m-1))/2;
		int c=t2;
		while(c>=0&&c<m){
			cout<<mat[i][c]<<" ";
			r.push_back(mat[i][c]);
			c=c+t1;
		}
	}
	cout<<endl;
	return r;
}

int main(){
	const int n=4,m=3;
	int arr1[m]={1,2,3};
	int arr2[m]={4,5,6};
	int arr3[m]={7,8,9};
	int arr4[m]={10,11,12};
	vector<int> a1(arr1,arr1+m);
	vector<int> a2(arr2,arr2+m);
	vector<int> a3(arr3,arr3+m);
	vector<int> a4(arr4,arr4+m);
	vector<vector<int> > mat;
	mat.push_back(a1);
	mat.push_back(a2);
	mat.push_back(a3);
	mat.push_back(a4);
	printMatrix(mat,n,m);
	system("pause");
}
```