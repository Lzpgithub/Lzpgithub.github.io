---
layout: post
title: 平均年龄(美团)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

已知某公司总人数为W，平均年龄为Y岁(每年3月末计算，同时每年3月初入职新人)，假设每年离职率为x，x>0&&x<1,每年保持所有员工总数不变进行招聘，新员工平均年龄21岁。
 
从今年3月末开始，请实现一个算法，可以计算出第N年后公司员工的平均年龄。(结果向上取整)。 

输入描述:

输入W Y x N

输出描述:

输出第N年后的平均年龄


#### 程序代码

```cpp
#include <iostream>

using namespace std;

int main(){
	int W,Y,N;
	float x;
	cin>>W>>Y>>x>>N;
	float y=Y;
	for(int i=0;i<N;i++){
		float lose=W*x;
		float remain=W-lose;
		y=(remain*y+remain+lose*21.0)/W;
	}
	int t=y;
	if(y-t>0)
		Y=t+1;
	else
		Y=t;

	cout<<Y<<" "<<y<<endl;

	system("pause");
}
```