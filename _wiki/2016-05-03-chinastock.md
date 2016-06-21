---
layout: post
title: 风口的猪-中国牛市(小米)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

风口之下，猪都能飞。当今中国股市牛市，真可谓“错过等七年”。 给你一个回顾历史的机会，已知一支股票连续n天的价格走势，以长度为n的整数数组表示，数组中第i个元素（prices[i]）代表该股票第i天的股价。 假设你一开始没有股票，但有至多两次买入1股而后卖出1股的机会，并且买入前一定要先保证手上没有股票。若两次交易机会都放弃，收益为0。 设计算法，计算你能获得的最大收益。 输入数值范围：2<=n<=100,0<=prices[i]<=100 

输入例子：

3,8,5,1,7,8

输出：

12

#### 程序代码


```cpp
#include <iostream>
#include <vector>

using namespace std;

int maxpolifie(vector<int> &prices,int x,int y){
	vector<int> temp;
	for(int i=x;i<=y-1;i++){
		for(int j=i+1;j<=y;j++){
			temp.push_back(prices[j]-prices[i]);
		}
	}
	int max=0;
	for(int i=0;i<temp.size();i++){
		if(max<temp[i])
			max=temp[i];
	}
	return max;
}

int calculateMax(vector<int> prices){
	vector<int> r;
	for(int i=1;i<prices.size();i++){
		int left=maxpolifie(prices,0,i);
		int right;
		if(i<=prices.size()-3)
			right=maxpolifie(prices,i+1,prices.size()-1);
		else
			right=0;
		r.push_back(left+right);
	}

	int max=0;
	for(int i=0;i<r.size();i++){
		if(max<r[i])
			max=r[i];
	}
	return max;
}

int main(){
	vector<int> prices;
	do{
		int n;
		cin>>n;
		prices.push_back(n);
	}while(getchar()==' ');

	cout<<calculateMax(prices)<<endl;

	system("pause");
}
```

