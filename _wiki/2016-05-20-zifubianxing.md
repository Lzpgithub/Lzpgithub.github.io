---
layout: post
title: “一战通offer”互联网实习季编程挑战
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 字符串变形

对于一个给定的字符串，我们需要在线性(也就是O(n))的时间里对它做一些变形。首先这个字符串中包含着一些空格，就像"Hello World"一样，然后我们要做的是把着个字符串中由空格隔开的单词反序，同时反转每个字符的大小写。比如"Hello World"变形后就变成了"wORLD hELLO"。

##### 输入描述:

给定一个字符串s以及它的长度n(1≤n≤500)


##### 输出描述:

请返回变形后的字符串。题目保证给定的字符串均由大小写字母和空格构成。

##### 输入例子:

"This is a sample",16

##### 输出例子:

"SAMPLE A IS tHIS"

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

string trans(string s,int n){
	int st=0;
	vector<string> strvec;
	int len;
	for(int i=0;i<n;i++){
		if(s[i]!=' '){
			if(s[i]<'a')
				s[i]=s[i]+32;
			else
				s[i]=s[i]-32;
		}else{
			len=i-st;
			string tp=s.substr(st,len);
			strvec.push_back(tp);
			st=i+1;
		}
	}
	len=n-st;                                      //最后一个单词
	string tp=s.substr(st,len);
	strvec.push_back(tp);

	cout<<strvec.size()<<endl;
	string newstr;
	int sz=strvec.size();
	for(int i=0;i<sz-1;i++){
		newstr=newstr+strvec.back()+" ";
		strvec.pop_back();
	}
	newstr=newstr+strvec.back();                 //最后一个单词没有空格
	//cout<<newstr<<endl;
	return newstr;
}

int main(){
	int n=16;
    string str="This is a sample";
	trans(str,n);

    system("pause");
}
```

#### 地域划分

现在有一块长条形的土地，这个土地我们可以看成是由n块小方格连接而成的(这些小方格我们可以将之编号为1到n)。而我们需要将其划分成两个部分，分别种上不同的作物(即作物A和B)，划分必须在某两个小方格之间进行，或者在土地的最左端或最右端，若划分在第i块到第i+1块间进行，则划分后，第1至第i块地种A，剩下的地种B。现在有一些专家对土地进行了检测，他们每个人评估了每块土地适合种的作物。请你找到一个合适的划分，使得其与所有专家的评估最吻合，也就是说，你划分到A而专家评估为B的次数和你划分到B而专家评估为A的次数之和最小。

##### 输入描述:

每组数据给定一个专家评估表land(其中0为评估A，1为评估B)，以及小块数量n(1≤n≤300)，专家评估次数m(1≤m≤300)


##### 输出描述:

请返回你的划分,即i和i+1。若在最左端，则输出0，1；在最右端则输出n,n+1。若有多解输出最靠左的划分。

##### 输入例子:

[[1,1,1,1],[0,0,0,0],[1,0,1,1]],4,3

##### 输出例子:

[0,1]


```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef struct{
	int pos;
	int count;
}Record;

bool cmp(const Record &rec1,const Record &rec2){
	if(rec1.count!=rec2.count)
		return rec1.count<rec2.count;
	else
		return rec1.pos<rec2.pos;
}


vector<int> getPartition(const vector<vector<int> > &land,int n,int m){
	int pre=-1;                           //记录前一次统计的数目
	vector<Record> vecrec;
	for(int i=0;i<=n;i++){                //分割线
		if(pre==-1){                      //第一次统计
			int sum=0;
			for(int x=0;x<m;x++){         //评审专家人数
				for(int y=0;y<n;y++){     //地的长度
					if(land[x][y]==0)     //从最左边开始统计
						sum++;            
				}
			}
			Record rec;
			rec.pos=0;
			rec.count=sum;
			vecrec.push_back(rec);
			pre=sum;                     //记录前一次统计的数目
		}else{
			int sum=0;
			for(int j=0;j<m;j++){
				if(land[j][i-1]==1)
					sum++;
			}
			int nixu=sum-(m-sum)+pre;
			Record rec;
			rec.pos=i;
			rec.count=nixu;
			vecrec.push_back(rec);
			pre=nixu;                     //记录前一次统计的数目
		}
	}
	sort(vecrec.begin(),vecrec.end(),cmp);
	Record rec=vecrec.front();
	//cout<<"["<<rec.pos<<","<<rec.pos+1<<"]"<<endl;
	vector<int> rs;
	rs.push_back(rec.pos);
	rs.push_back(rec.pos+1);
	return rs;
}

int main(){
	int n=4,m=3;
	int arr1[4]={1,1,1,1};
	vector<int> vec1(arr1,arr1+4);
	int arr2[4]={0,0,0,0};
	vector<int> vec2(arr2,arr2+4);
	int arr3[4]={1,0,1,1};
	vector<int> vec3(arr3,arr3+4);
	vector<vector<int> > land;
	land.push_back(vec1);
	land.push_back(vec2);
	land.push_back(vec3);

	getPartition(land,n,m);

	system("pause");
}
```