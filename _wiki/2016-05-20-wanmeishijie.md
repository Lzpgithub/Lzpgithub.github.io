---
layout: post
title: 完美世界2017实习生笔试题
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 小萌的包裹

小萌是个WOW发烧友，每天都痴迷于他的法师号。精诚所至金石为开，小萌穿越到WOW的世界中了...
初来乍到的小萌在暴风城的小巷中，遇见了一位善良的德鲁伊。德鲁伊听了小萌的故事，打算帮助他在WOW这个世界好好活下去，于是，把自己的东西都给了小萌了...

德鲁伊的东西太多了，于是小萌去拍卖行买了几个包裹，一切妥当之后，小萌开始把东西装进包裹里。
不过，因为小萌穿越时候脑袋先着地，所以脑子不好用，每次他拿起一个物品，要不装进包里，要不就直接扔掉...

而且，一个背包一旦不往里装东西，小萌就会封上口不再用...

现在，告诉你小萌每个物品的体积，背包的个数和容量，以及小萌拿物品的顺序，你要帮助小萌求出他能拿走多少东西。

##### 输入描述:

输入的第一行为一个整数T，代表测试数据组数。

  第一行：三个正整数 N、T、M。 分别表示物品的个数，背包的容量，背包个数。

  第二行：N个数。表示每个物品的体积。

  保证：

  1<=N,T,M<=20,0<=vi<=30 

##### 输出描述:

对于每组数据，输出一个整数，代表小萌可以拿走的最多物品个数。

##### 输入例子:

2

5 5 2

4 3 4 2 1

##### 输出例子:

3

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main(){
	int N,T,M;                 //N为物品个数  T为每个背包容量  M为背包的个数
	while(cin>>N>>T>>M){
		vector<int> arr;
		for(int i=0;i<N;i++){
			int num;
			cin>>num;
			arr.push_back(num);
		}
		sort(arr.begin(),arr.end());         //排序

		int sum=0;
		int count=0;                         //装入物品计数
		for(int i=0;i<N;i++){
			sum=sum+arr[i];
			if(sum<=T){                      //判断是否大于包的容量
					count++;
			}else{
				M--;
				sum=arr[i];
				if(sum>T||M<1){              //判断第i个物品是否大于包的容量或者没有包
					break;
				}else{
					count++;
				}
			}
		}
		cout<<count<<endl;
	}
}
```