---
layout: post
title: 扎金花
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

### 题目描述

两个搜狐的程序员加了一个月班，终于放假了，于是他们决定扎金花渡过愉快的假期 。

游戏规则：

共52张普通牌，牌面为2,3,4,5,6,7,8,9,10,J,Q,K,A之一，大小递增，各四张； 每人抓三张牌。两人比较手中三张牌大小，大的人获胜。 

对于牌型的规则如下： 

1.三张牌一样即为豹子 

2.三张牌相连为顺子（A23不算顺子） 

3.有且仅有两张牌一样为对子 豹子>顺子>对子>普通牌型 在牌型一样时，比较牌型数值大小（如AAA>KKK,QAK>534，QQ2>10104） 在二人均无特殊牌型时，依次比较三张牌中最大的。大的人获胜，如果最大的牌一样，则比较第二大，以此类推（如37K>89Q） 如二人牌面相同，则为平局。

### 输入描述

输入两个字符串代表两个玩家的牌（如"10KQ" "354"），先输入的作为玩家1，后输入的作为玩家2

### 输入例子

KQ3 3Q9

10QA 6102

5810 7KK

632 74J

10102 K77

JKJ 926

68K 27A

### 输出例子

1

1

-1

-1

1

1

-1


### 程序代码

```cpp
#include <string>                     //在vs2010里面必须包含进来
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

void convertNum(string s1,string s2,vector<int> &i1,vector<int> &i2){   //将字符串转换为整数
	int len1=s1.length();             //转换字符串1
	for(int i=0;i<len1;i++){
		if(s1[i]!='1'){               //如果字符不为1
			if(s1[i]=='J'){
				i1.push_back(11);
			}else if(s1[i]=='Q'){
				i1.push_back(12);
			}else if(s1[i]=='K'){
				i1.push_back(13);
			}else if(s1[i]=='A'){
				i1.push_back(14);
			}else{
				int num=s1[i]-'0';
				i1.push_back(num);
			}
		}else{                                  //如果字符为1 则分两种情况进行讨论
			if((i+1)<len1){                     //判断是否超过长度
				if(s1[i+1]=='0'){               //判断后面一个字符是否为0
					i1.push_back(10);
					i++;                        //跳过一个字符
				}else{
					i1.push_back(1);
				}
			}else{
				i1.push_back(1);
			}
		}
	}

	int len2=s2.length();             //转换字符串2
	for(int i=0;i<len2;i++){
		if(s2[i]!='1'){
			if(s2[i]=='J'){
				i2.push_back(11);
			}else if(s2[i]=='Q'){
				i2.push_back(12);
			}else if(s2[i]=='K'){
				i2.push_back(13);
			}else if(s2[i]=='A'){
				i2.push_back(14);
			}else{
				int num=s2[i]-'0';
				i2.push_back(num);
			}
		}else{                                  //如果字符为1 则分两种情况进行讨论
			if((i+1)<len2){                     //判断是否超过长度
				if(s2[i+1]=='0'){               //判断后面一个字符是否为0
					i2.push_back(10);
					i++;                        //跳过一个字符
				}else{
					i2.push_back(1);
				}
			}else{
				i2.push_back(1);
			}
		}
	}
}

void printvector(vector<int> v){              //打印向量
	for(int i=0;i<v.size();i++){
		cout<<v[i]<<" ";
	}
	cout<<endl;
}

bool inputCheck(vector<int> v){               //输入合法性检查
	if(v.size()==3){                          //数据大小检查
		for(int i=0;i<2;i++){                 //数据范围检查
			if(v[i]>14||v[i]<2)              
				return false;
		}
		return true;
	}else
		return false;
}

int paiXing(vector<int> v){                   //牌型判断
	if(v[0]==v[1]&&v[2]==v[0])                //豹子
		return 4;

	sort(v.begin(),v.end());                  //进行一次排序
	if(v[0]+1==v[1]&&v[1]+1==v[2]){           //顺子
		return 3;
	}

	if(v[0]==v[1]||v[1]==v[2]||v[0]==v[2])                //对子
		return 2;

	return 1;                                //普通牌型
}


int main(){
	string s1,s2;
	while(cin>>s1>>s2){
		vector<int> i1;
		vector<int> i2;
		convertNum(s1,s2,i1,i2);               //字符串转换为整数
		if(inputCheck(i1)&&inputCheck(i2)){    //输入合法性检查
			int p1=paiXing(i1);
			int p2=paiXing(i2);
			if(p1==p2){                        //牌型相同的情况下进行比较
				if(p1>1){                      //如果是特殊牌型
					if(p1==4){                      //如果是豹子
						if(i1[0]>i2[0])             //只需比较第一个数
							cout<<"1"<<endl;
						else if(i1[0]<i2[0])
							cout<<"-1"<<endl;
						else
							cout<<"0"<<endl;
					}else if(p1==3){                 //如果是顺子
						sort(i1.begin(),i1.end());   //先进行排序
						sort(i2.begin(),i2.end());
						if(i1[2]>i2[2])              //比较最后一个元素
							cout<<"1"<<endl;
						else if(i1[2]<i2[2])
							cout<<"-1"<<endl;
						else
							cout<<"0"<<endl;
					}else{                          //如果是对子
						int num1,n1,num2,n2;
						if(i1[0]==i1[1]){           //取出对子和单数
							num1=i1[0];
							n1=i1[2];
						}else if(i1[0]==i1[2]){
							num1=i1[0];
							n1=i1[1];
						}else{
							num1=i1[1];
							n1=i1[0];
						}

						if(i2[0]==i2[1]){
							num2=i2[0];
							n2=i2[2];
						}else if(i2[0]==i2[2]){
							num2=i2[0];
							n2=i2[1];
						}else{
							num2=i2[1];
							n2=i2[0];
						}

						if(num1>num2){                 //顺子比较
							cout<<"1"<<endl;
						}else if(num1<num2){
							cout<<"-1"<<endl;
						}else{
							if(n1>n2)
								cout<<"1"<<endl;
							else if(n1<n2)
								cout<<"-1"<<endl;
							else
								cout<<"0"<<endl;
						}
					}
				}else{                                //如果是普通牌型
					sort(i1.begin(),i1.end());        //先进行排序
					sort(i2.begin(),i2.end());
					int j;
					for(j=2;j>=0;j--){
						if(i1[j]!=i2[j]){
							if(i1[j]>i2[j])
								cout<<"1"<<endl;
							else
								cout<<"-1"<<endl;
							break;
						}
					}
					if(j<0){
						cout<<"0"<<endl;
					}
				}
			}else{                                  //牌型不同的情况下进行比较
				if(p1>p2)
					cout<<"1"<<endl;
				else if(p1<p2)
					cout<<"-1"<<endl;
				else
					cout<<"0"<<endl;
			}
		}else{
			cout<<"-2"<<endl;                    //输出不合法
		}
	
	}
	system("pause");
}

```