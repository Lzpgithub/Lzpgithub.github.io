---
layout: post
title: 顺时针旋转矩阵
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

有一个NxN整数矩阵，请编写一个算法，将矩阵顺时针旋转90度。
给定一个NxN的矩阵，和矩阵的阶数N,请返回旋转后的NxN矩阵,保证N小于等于300。

测试样例：

[[1,2,3],[4,5,6],[7,8,9]],3

返回：[[7,4,1],[8,5,2],[9,6,3]]

#### 程序代码

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<vector<int> > rotateMatrix(vector<vector<int> > mat,int n){
	vector<vector<int> > r;
	r=mat;
	for(int i=0;i<n;i++){
		for(int j=0;j<n;j++){
			r[j][n-i-1]=mat[i][j];
		}
	}
	for(int i=0;i<n;i++){
		for(int j=0;j<n;j++){
			cout<<r[i][j]<<" ";
		}
		cout<<endl;
	}
	return r;
}

int main(){
	int arr1[3]={1,2,3};
	int arr2[3]={4,5,6};
	int arr3[3]={7,8,9};
	vector<int> array1(arr1,arr1+3);
	vector<int> array2(arr2,arr2+3);
	vector<int> array3(arr3,arr3+3);
	vector<vector<int> > mat;
	mat.push_back(array1);
	mat.push_back(array2);
	mat.push_back(array3);
	int n=3;
	rotateMatrix(mat,n);

	system("pause");
}
```