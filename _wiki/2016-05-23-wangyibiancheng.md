---
layout: post
title: 网易内推笔试编程题合集（二）
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 不要二

二货小易有一个W*H的网格盒子，网格的行编号为0~H-1，网格的列编号为0~W-1。每个格子至多可以放一块蛋糕，任意两块蛋糕的欧几里得距离不能等于2。

对于两个格子坐标(x1,y1),(x2,y2)的欧几里得距离为:

**( (x1-x2) * (x1-x2) + (y1-y2) * (y1-y2) ) **的算术平方根

小易想知道最多可以放多少块蛋糕在网格盒子里。 

##### 输入描述:

每组数组包含网格长宽W,H，用空格分割.(1 ≤ W、H ≤ 1000)


##### 输出描述:

输出一个最多可以放的蛋糕数

##### 输入例子:

3 2

##### 输出例子:

4

```cpp
#include <iostream>

using namespace std;

int main(){
	int w,h;
	while(cin>>w>>h){
		int sum=0;
		for(int i=0;i<w;i++){
			for(int j=0;j<h;j++){
				if((i%4==0||i%4==1)&&(j%4==0||j%4==1))
					sum++;
				if((i%4==2||i%4==3)&&(j%4==2||j%4==3))
					sum++;
			}
		}
		cout<<sum<<endl;
	}
}
```

#### 解救小易

有一片1000*1000的草地，小易初始站在(1,1)(最左上角的位置)。小易在每一秒会横向或者纵向移动到相邻的草地上吃草(小易不会走出边界)。大反派超超想去捕捉可爱的小易，他手里有n个陷阱。第i个陷阱被安置在横坐标为xi ，纵坐标为yi 的位置上，小易一旦走入一个陷阱，将会被超超捕捉。你为了去解救小易，需要知道小易最少多少秒可能会走入一个陷阱，从而提前解救小易。 

##### 输入描述:

第一行为一个整数n(n ≤ 1000)，表示超超一共拥有n个陷阱。

第二行有n个整数xi，表示第i个陷阱的横坐标

第三行有n个整数yi，表示第i个陷阱的纵坐标

保证坐标都在草地范围内。


##### 输出描述:

输出一个整数,表示小易最少可能多少秒就落入超超的陷阱

##### 输入例子:

3

4 6 8

1 2 1

##### 输出例子:

3

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main(){
	int n;
	while(cin>>n){
		vector<int> arr;
		int num;
		for(int i=0;i<n;i++){
			cin>>num;
			arr.push_back(num);
		}
		int temp;
		for(int i=0;i<n;i++){
			cin>>num;
			temp=arr[i]-1+num-1;
			arr[i]=temp;
		}
		sort(arr.begin(),arr.end());
		cout<<arr[0]<<endl;
	}
}
```

#### 统计回文

“回文串”是一个正读和反读都一样的字符串，比如“level”或者“noon”等等就是回文串。花花非常喜欢这种拥有对称美的回文串，生日的时候她得到两个礼物分别是字符串A和字符串B。现在她非常好奇有没有办法将字符串B插入字符串A使产生的字符串是一个回文串。你接受花花的请求，帮助她寻找有多少种插入办法可以使新串是一个回文串。如果字符串B插入的位置不同就考虑为不一样的办法。

例如：

A = “aba”，B = “b”。这里有4种把B插入A的办法：

* 在A的第一个字母之前: "baba" 不是回文 
* 
* 在第一个字母‘a’之后: "abba" 是回文 
* 
* 在字母‘b’之后: "abba" 是回文 
* 
* 在第二个字母'a'之后 "abab" 不是回文 

所以满足条件的答案为2 

##### 输入描述:

每组输入数据共两行。

第一行为字符串A

第二行为字符串B

字符串长度均小于100且只包含小写字母


##### 输出描述:

输出一个数字，表示把字符串B插入字符串A之后构成一个回文串的方法数

##### 输入例子:

aba

b

##### 输出例子:

2


```cpp
#include <iostream>
#include <string>

using namespace std;

bool ishuiwei(string str,int len){
	int s=len/2;
	for(int i=0;i<s;i++){
		if(str[i]!=str[len-i-1])
			return false;
	}
	return true;
}

int main(){
	string str1,str2;
	while(cin>>str1>>str2){
		int len1=str1.length();
		int len=len1+str2.length();
		int sum=0;
		for(int i=0;i<=len1;i++){
			string temp=str1;
			temp.insert(i,str2);
			if(ishuiwei(temp,len))
				sum++;
		}
		cout<<sum<<endl;
	}
}
```


#### 小易喜欢的单词

小易喜欢的单词具有以下特性：

1.单词每个字母都是大写字母

2.单词没有连续相等的字母

3.单词没有形如“xyxy”(这里的x，y指的都是字母，并且可以相同)这样的子序列，子序列可能不连续。

例如：

小易不喜欢"ABBA"，因为这里有两个连续的'B'

小易不喜欢"THETXH"，因为这里包含子序列"THTH"

小易不喜欢"ABACADA"，因为这里包含子序列"AAAA"

小易喜欢"A","ABA"和"ABCBA"这些单词，给你一个单词，你要回答小易是否会喜欢这个单词。 

##### 输入描述:

输入为一个字符串，都由大写字母组成，长度小于100


##### 输出描述:

如果小易喜欢输出"Likes",不喜欢输出"Dislikes"

##### 输入例子:

AAA

##### 输出例子:

Dislikes

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <map>
#include <algorithm>

using namespace std;

int main(){
	string str;
	while(cin>>str){
		int lenstr=str.length();
	
		bool stoprun=false;                       //停止运行标志
		map<char,int> record;
		char pre;
		for(int i=0;i<lenstr;i++){
			if(i!=0){
				if(pre==str[i]){                  //判断是否连续重复
					cout<<"Dislikes"<<endl;
					stoprun=true;
					break;
				}
			}
			record[str[i]]++;
			pre=str[i];
		}

		if(!stoprun){
			string strrp;
			for(int i=0;i<lenstr;i++){             //把重复出现的字母取出
				if(record[str[i]]>=2)
					strrp=strrp+str[i];
			}

			int lenstrrp=strrp.length();
			int loop1=lenstrrp-4;
			int loop2=lenstrrp-3;
			int m=0;
			for(int i1=0;i1<=loop1;i1++){             //确定4个字母中的第一个字母
				for(int j=i1+1;j<=loop2;j++){         //确定4个字母中的第二个字母
					m=0;
					for(int k=j+1;k<lenstrrp;k++){
						if(strrp[k]==strrp[i1]&&m==0) //确定4个字母中的第三个字母
							m++;
						else if(strrp[k]==strrp[j]&&m==1){  //确定4个字母中的第四个字母
							cout<<"Dislikes"<<endl;
							stoprun=true;
							break;
						}
					}
					if(stoprun)
						break;
				}
				if(stoprun)
						break;
			}

			if(!stoprun)
				cout<<"Likes"<<endl;
		}
	}
}
```