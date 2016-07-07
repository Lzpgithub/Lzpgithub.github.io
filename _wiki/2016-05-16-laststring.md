---
layout: post
title: 字符串最后一个单词的长度(华为)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

计算字符串最后一个单词的长度，单词以空格隔开。

#### 输入描述:

一行字符串。

#### 输出描述:

整数N，最后一个单词的长度。

#### 输入例子:

hello world

#### 输出例子:

5

#### 程序代码

```cpp
#include <iostream>
#include <string>

using namespace std;

int main(){
	string str;
	while(getline(cin,str)){
		int c=0;
		int len=str.length();
		for(int i=len-1;i>0;i--){
			if(str[i] == ' '){
				c=i;
				break;
			}
		}
		string substring;
		if(c==0){
			substring=str.substr(c,len-c);
			cout<<substring.length()<<endl;
		}
		else{
			substring=str.substr(c+1,len-c);
			cout<<substring.length()<<endl;
		}
	}
	system("pause");
}
```