---
layout: post
title: 直方图内最大矩形(美团)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

有一个直方图，用一个整数数组表示，其中每列的宽度为1，求所给直方图包含的最大矩形面积。比如，对于直方图[2,7,9,4],它所包含的最大矩形的面积为14(即[7,9]包涵的7x2的矩形)。

给定一个直方图A及它的总宽度n，请返回最大矩形面积。保证直方图宽度小于等于500。保证结果在int范围内。

测试样例：

[2,7,9,4,1],5

返回：14

#### 程序代码

```cpp
#include <iostream>
#include <vector>

using namespace std;

int countArea(vector<int> A,int n){
	vector<int> r;
	for(int i=0;i<n;i++){
		int c=1;
		for(int j=i+1;j<n;j++){
			if(A[i]<=A[j])
				c++;
			else
				break;
		}
		for(int j=i-1;j>=0;j--){
			if(A[i]<=A[j])
				c++;
			else
				break;
		}
		r.push_back(A[i]*c);
	}
	

	int max=0;
	for(int i=0;i<r.size();i++){
		if(max<r[i])
			max=r[i];
	}
	return max;
}

int main(){
	vector<int> A;
	//A.push_back(2);
	//A.push_back(7);
	//A.push_back(9);
	//A.push_back(4);
	A.push_back(281);
	A.push_back(179);
	A.push_back(386);
	A.push_back(165);
	A.push_back(88);
	A.push_back(500);
	int n=6;
	cout<<countArea(A,n)<<endl;
	system("pause");
}
```