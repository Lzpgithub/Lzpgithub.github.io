---
layout: post
title: 进制转换（华为）
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

写出一个程序，接受一个十六进制的数值字符串，输出该数值的十进制字符串。（多组同时输入 ）

#### 输入描述:

输入一个十六进制的数值字符串。


#### 输出描述:

输出该数值的十进制字符串。

#### 输入例子:

0xA

#### 输出例子:

10

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <math.h>

using namespace std;

int main(){
	string str;
	while(cin>>str){
		vector<int> erjin;
		for(int i=2;i<str.length();i++){
			int num;
			if(str[i]=='A')
				num=10;
			else if(str[i]=='B')
				num=11;
			else if(str[i]=='C')
				num=12;
			else if(str[i]=='D')
				num=13;
			else if(str[i]=='E')
				num=14;
			else if(str[i]=='F')
				num=15;
			else
				num=str[i]-'0';

			int len=0;
			vector<int> temp;
			while(num>0){                  //转换为二进制
				temp.push_back(num%2);
				len++;
				num=num/2;
			}
			for(int i=0;i<4-len;i++){       //补零
				temp.push_back(0);
			}

			for(int i=0;i<4;i++)            //倒置
				erjin.push_back(temp[3-i]);
		}

		int sum=0;
		int size=erjin.size();
		for(int i=0;i<size;i++)
			sum=sum+pow(2.0,i)*erjin[size-i-1];
		cout<<sum<<endl;
	}
}
```