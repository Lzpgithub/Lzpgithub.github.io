---
layout: post
title: 乐视2017暑期实习生笔试题（二）
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 偶数大翻转

今天的计算机课上，老师给同学们出了一道题：

输入n个数，请将其中的偶数的二进制反转。

eg：输入1 6 5

其中6是偶数，二进制表示为110，反转后为011，代表3，所以最终输出1 3 5。小贱君最近脑子不怎么好使，想了半天也没想出来如何做，最后他向你寻求帮助了，帮帮可怜的小贱君吧！

##### 输入描述:

输入包含多组测试数据。

对于每组测试数据：

N --- 输入的数字个数

N个数：a0,a1,...,an-1

保证：

1<=N<=3000,0<=ai<=INT_MAX.

##### 输出描述:

对于每组数据，输出N个整数。


##### 输入例子:

5

1 3 10 6 7

6

26 52 31 45 82 34

##### 输出例子:

1 3 5 3 7

11 11 31 45 37 17


```cpp
#include <iostream>
#include <vector>
#include <math.h>
using namespace std;

int reverse(int num){
	vector<int> erjin;
	while(num){
		erjin.push_back(num%2);
		num=num/2;
	}
	int sum=0;
	int len=erjin.size();
	for(int i=0;i<len;i++){
		sum=sum+erjin[len-i-1]*pow(2.0,i);
	}
	return sum;
}

int main(){
	int n;
	while(cin>>n){
		vector<int> arr;
		for(int i=0;i<n;i++){
			int num;
			cin>>num;
			if(num%2==0)
				arr.push_back(reverse(num));
			else
				arr.push_back(num);
		}

		for(int i=0;i<n;i++){
			cout<<arr[i]<<" ";
			if (i != n - 1) cout<<" ";   //牛客网那个傻逼编译器打印最后一个元素不能输出空格
		}
		cout<<endl;
	}
}
```