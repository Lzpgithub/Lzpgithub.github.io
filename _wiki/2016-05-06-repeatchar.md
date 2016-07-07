---
layout: post
title: 首个重复字符(去哪儿)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

对于一个字符串，请设计一个高效算法，找到第一次重复出现的字符。

给定一个字符串(不一定全为字母)A及它的长度n。请返回第一个重复出现的字符。保证字符串中有重复字符，字符串的长度小于等于500。

测试样例：

"qywyer23tdd",11

返回：y

#### 程序代码

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

bool repeat(vector<char> &str,char c){
	for(int i=0;i<str.size();i++){
		if(str[i]==c)
			return true;
	}
	return false;
}

char findFirstRepeat(string A,int n){
	vector<char> str;
	for(int i=0;i<n;i++){
		if(!repeat(str,A[i]))
			str.push_back(A[i]);
		else
			return A[i];
	}
	return 0;
}


int main(){
	string A="qywyer23tdd";
	int n=11;
	cout<<findFirstRepeat(A,n)<<endl;
	system("pause");
}
```

