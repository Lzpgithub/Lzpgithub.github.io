---
layout: post
title: 计算字符个数(华为)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

写出一个程序，接受一个有字母和数字以及空格组成的字符串，和一个字符，然后输出输入字符串中含有该字符的个数。不区分大小写。

#### 输入描述:

输入一个有字母和数字以及空格组成的字符串，和一个字符。

#### 输出描述:

输出输入字符串中含有该字符的个数。

#### 输入例子:

ABCDEF

A

#### 输出例子:

1

#### 程序代码

```cpp
#include <iostream>
#include <string>

using namespace std;

int main(){
	string str;
	string c;
	while(getline(cin,str)){
		getline(cin,c);
		int count=0;
		int len=str.length();
		for(int i=0;i<len;i++){
			if(str[i]==c[0]){
				count++;
			}
		}
		cout<<count<<endl;
	}
	system("pause");
}
```