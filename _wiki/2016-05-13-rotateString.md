---
layout: post
title: 字符串的旋转
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

对于一个字符串，和字符串中的某一位置，请设计一个算法，将包括i位置在内的左侧部分移动到右边，将右侧部分移动到左边。

给定字符串A和它的长度n以及特定位置p，请返回旋转后的结果。

测试样例：

"ABCDEFGH",8,4

返回："FGHABCDE"


#### 程序代码

```cpp
#include <iostream>
#include <string>

using namespace std;

string rotaString(string A,int n,int p){
	string strleft=A.substr(0,p+1);
	string strright=A.substr(p+1,n-p);
	string str=strright+strleft;
	return str;
}

int main(){
	string A="ABCDEFGH";
	int n=8; 
	int p=4;
	string str=rotaString(A,n,p);
	cout<<str<<endl;

	system("pause");
}
```