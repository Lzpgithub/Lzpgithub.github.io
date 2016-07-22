---
layout: post
title: 字符串分隔（华为）
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

•连续输入字符串，请按长度为8拆分每个字符串后输出到新的字符串数组；

•长度不是8整数倍的字符串请在后面补数字0，空字符串不处理。

#### 输入描述:

连续输入字符串(输入2次,每个字符串长度小于100)


#### 输出描述:

输出到长度为8的新字符串数组

#### 输入例子:

abc

123456789

#### 输出例子:

abc00000

12345678

90000000

```cpp
#include <iostream>
#include <string>

using namespace std;

int main(){
	string str1,str2;
	while(cin>>str1>>str2){
		int len1=str1.length();
		int len2=str2.length();

		int n1=len1/8;
		int y1=len1%8;
		for(int i=0;i<n1;i++){
			string strtemp=str1.substr(i*8,8);
			cout<<strtemp<<endl;
		}
		if(y1!=0){
			string strtemp=str1.substr(n1*8,y1);
			for(int i=0;i<8-y1;i++)
				strtemp=strtemp+'0';
			cout<<strtemp<<endl;
		}

		int n2=len2/8;
		int y2=len2%8;
		for(int i=0;i<n2;i++){
			string strtemp=str2.substr(i*8,8);
			cout<<strtemp<<endl;
		}
		if(y2!=0){
			string strtemp=str2.substr(n2*8,y2);
			for(int i=0;i<8-y2;i++)
				strtemp=strtemp+'0';
			cout<<strtemp<<endl;
		}
	}
	system("pause");
}
```