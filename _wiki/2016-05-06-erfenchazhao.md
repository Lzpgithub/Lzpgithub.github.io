---
layout: post
title: 二分查找(去哪儿)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

对于一个有序数组，我们通常采用二分查找的方式来定位某一元素，请编写二分查找的算法，在数组中查找指定元素。

给定一个整数数组A及它的大小n，同时给定要查找的元素val，请返回它在数组中的位置(从0开始)，若不存在该元素，返回-1。若该元素出现多次，请返回第一次出现的位置。

测试样例：

[1,3,5,7,9],5,3

返回：1

#### 程序代码

```cpp
#include <iostream>
#include <vector>

using namespace std;

int getPos(vector<int> A,int n,int val){
	int low=0;
	int high=n-1;
	int mid=(low+high)/2;
	while(A[mid]!=val){
		if(A[mid]>val)
			high=mid-1;
		else
			low=mid+1;
		mid=(low+high)/2;
		if(low>high)
			return -1;
	}

	while(mid>0&&A[mid-1]==val){
		mid--;
	}
	return mid;
}

int main(){
	int n=5;
	int val=7;
	vector<int> A;
	A.push_back(1);
	A.push_back(7);
	A.push_back(7);
	A.push_back(7);
	A.push_back(9);

	cout<<getPos(A,n,val)<<endl;
	system("pause");
}
```

