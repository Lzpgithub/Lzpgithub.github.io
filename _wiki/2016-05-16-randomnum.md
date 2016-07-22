---
layout: post
title: 明明的随机数（华为）
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

明明想在学校中请一些同学一起做一项问卷调查，为了实验的客观性，他先用计算机生成了N个1到1000之间的随机整数（N≤1000），对于其中重复的数字，只保留一个，把其余相同的数去掉，不同的数对应着不同的学生的学号。然后再把这些数从小到大排序，按照排好的顺序去找同学做调查。请你协助明明完成“去重”与“排序”的工作。
 
Input Param：

n输入随机数的个数   
  
inputArray：n个随机整数组成的数组 
     
Return Value

OutputArray输出处理后的随机整数
 

注：测试用例保证输入参数的正确性，答题者无需验证。测试用例不止一组。


#### 输入描述：

输入多行，先输入随机整数的个数，再输入相应个数的整数


#### 输出描述：

返回多行，处理后的结果

#### 输入例子：
11

10

20

40

32

67

40

20

89

300

400

15

#### 输出例子：

10

15

20

32

40

67

89

300

400

#### 程序代码

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main(){
	int n;
	while(cin>>n){
		vector<int> inputarr;
		for(int i=0;i<n;i++){   //输入
			int num;
			cin>>num;
			bool b=true;
			for(int j=0;j<inputarr.size();j++){
				if(inputarr[j]==num){
					b=false;
					break;
				}
			}

			if(b)              //判断是否重复数字
				inputarr.push_back(num);
		}

		int len=inputarr.size();
		for(int i=0;i<len-1;i++){        //进行选择排序
			int min=inputarr[i];
			int pos=i;
			for(int j=i+1;j<len;j++){
				if(min>inputarr[j]){
					min=inputarr[j];
					pos=j;
				}
			}
			if(pos!=i){                  //交换
				int temp=inputarr[i];
				inputarr[i]=inputarr[pos];
				inputarr[pos]=temp;
			}
		}

		for(int i=0;i<len;i++){        //打印数组
			cout<<inputarr[i]<<endl;
		}
	}
	system("pause");
}
```