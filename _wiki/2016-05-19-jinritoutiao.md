---
layout: post
title: 今日头条2017客户端工程师实习生笔试题
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 回文解码

现在有一个字符串，你要对这个字符串进行 n 次操作，每次操作给出两个数字：(p, l) 表示当前字符串中从下标为 p 的字符开始的长度为 l 的一个子串。你要将这个子串左右翻转后插在这个子串原来位置的正后方，求最后得到的字符串是什么。字符串的下标是从 0 开始的，你可以从样例中得到更多信息。

##### 输入描述:

每组测试用例仅包含一组数据，每组数据第一行为原字符串，长度不超过 10 ，仅包含大小写字符与数字。接下来会有一个数字 n 表示有 n 个操作，再接下来有 n 行，每行两个整数，表示每次操作的(p , l)。


保证输入的操作一定合法，最后得到的字符串长度不超过 1000。

##### 输出描述:

输出一个字符串代表最后得到的字符串。

##### 输入例子:

ab

2

0 2

1 3

##### 输出例子:

abbaabb


```cpp
#include <iostream>
#include <string>

using namespace std;

string reverseStr(string str){
	int len=str.size();
	string nstr;
	for(int i=len-1;i>=0;i--){
		nstr=nstr+str[i];
	}
	return nstr;
}

int main(){
	int n;
	string str;
	while(cin>>str>>n){
		for(int i=0;i<n;i++){
			int p,l;
			cin>>p>>l;
			string strtemp=reverseStr(str.substr(p,l));
			str.insert(p+l,strtemp);
		}
		cout<<str<<endl;
	}
}
```


#### 出专辑

你作为一名出道的歌手终于要出自己的第一份专辑了，你计划收录 n 首歌而且每首歌的长度都是 s 秒，每首歌必须完整地收录于一张 CD 当中。每张 CD 的容量长度都是 L 秒，而且你至少得保证同一张 CD 内相邻两首歌中间至少要隔 1 秒。为了辟邪，你决定任意一张 CD 内的歌数不能被 13 这个数字整除，那么请问你出这张专辑至少需要多少张 CD ？

##### 输入描述:

每组测试用例仅包含一组数据，每组数据第一行为三个正整数 n, s, L。 保证 n ≤ 100 , s ≤ L ≤ 10000 


##### 输出描述:

输出一个整数代表你至少需要的 CD 数量。

##### 输入例子:

7 2 6

##### 输出例子:

4

注意特殊例子：（26 1 100），（27 1 27）

```cpp
#include <iostream>
#include <string>

using namespace std;

int main(){
	int n,s,L;
	while(cin>>n>>s>>L){
		int x=L/s;
		int coutS=x*s+x-1;
		while(coutS>L||x%13==0){     //限定每张CD歌数不是13的倍数
			x--;
			coutS=x*s+x-1;
		}
		int minsum=n/x;
		int yunum=n%x;
		if(yunum!=0){                //判断余数是否为13的倍数
			if(yunum%13==0){
				if(minsum==0){       //判断前面是否有光碟
					minsum++;
				}else{
					int i=1;
					while((minsum-i)%13==0)  //从前面的光碟中抽出一些歌曲到最后一张光碟中 但必须保证剩下的数目不是13的倍数
						i++;
					if((yunum+i)*s+yunum+i>L)
						minsum++;
				}
			}
			minsum++;
		}
		cout<<minsum<<endl;
	}
}
```

#### 魔法权值
给出 n 个字符串，对于每个 n 个排列 p，按排列给出的顺序(p[0] , p[1]...p[n-1])依次连接这 n 个字符串都能得到一个长度为这些字符串长度之和的字符串。所以按照这个方法一共可以生成 n! 个字符串。

一个字符串的权值等于把这个字符串循环左移 i 次后得到的字符串仍和原字符串全等的数量，i 的取值为 [1 , 字符串长度]。求这些字符串最后生成的 n! 个字符串中权值为 K 的有多少个。

注：定义把一个串循环左移 1 次等价于把这个串的第一个字符移动到最后一个字符的后面。

##### 输入描述:

每组测试用例仅包含一组数据，每组数据第一行为两个正整数 n, K ， n 的大小不超过 8 ， K 不超过 200。接下来有 n 行，每行一个长度不超过 20 且仅包含大写字母的字符串。


##### 输出描述:

输出一个整数代表权值为 K 的字符串数量。

##### 输入例子:

3 2

AB

RAAB

RA

##### 输出例子:

3

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

bool kweight(string str,int len,int k){       //判断字符串是否等于k值
	int count=0;
	string oldstr=str;
	for(int i=1;i<=len;i++){
		char c=str[len-1];
		str.erase(len-1,1);
		str.insert(0,1,c);
		if(str==oldstr)
			count++;
	}
	if(k==count)
		return true;
	else
		return false;
}

int main(){
	int n;
	int k;
	while(cin>>n>>k){
		vector<string> arrstr;
		int sumstrlen=0;
		for(int i=0;i<n;i++){                 //输入数据并计算总的字符串长度
			string strtemp;
			cin>>strtemp;
			arrstr.push_back(strtemp);
			sumstrlen=sumstrlen+strtemp.length();
		}

		vector<string> arr;                   //插入法求全排列
		string str="0";
		arr.push_back(str);
		for(int i=1;i<n;i++){
			int len=arr.size();
			for(int j=0;j<len;j++){
				char c=i+'0';
				for(int k=1;k<=i;k++){
					string strtemp=arr[j];
					strtemp.insert(k,1,c);
					arr.push_back(strtemp);
				}
				arr[j].insert(0,1,c);
			}
		}

		int sum=0;
		int len=arr.size();
		for(int i=0;i<len;i++){
			string newstr;
			for(int j=0;j<n;j++){
				newstr=newstr+arrstr[arr[i][j]-'0'];
			}
			if(kweight(newstr,sumstrlen,k))
				sum++;
		}
		cout<<sum<<endl;
	}
}
```

#### 或与加

给定 x, k ，求满足 x + y = x | y 的第 k 小的正整数 y 。 | 是二进制的或(or)运算，例如 3 | 5 = 7。
比如当 x=5，k=1时返回 2，因为5+1=6 不等于 5|1=5，而 5+2=7 等于 5 | 2 = 7。

##### 输入描述:

每组测试用例仅包含一组数据，每组数据为两个正整数 x , k。 满足 0 < x , k ≤ 2,000,000,000。

##### 输出描述:

输出一个数y。

##### 输入例子:

5 1

##### 输出例子:

2

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main(){
	long long x,k;
	while(cin>>x>>k){
		long long bit=1;
		long long count=0;
		vector<long long> arr;
		while(count<k){
			long long temp=bit&x;
			if(temp==0){
				arr.push_back(bit);
				for(int i=0;i<count;i++){
					arr.push_back(bit|arr[i]);
				}
				count=2*count+1;
			}
			bit=bit<<1;
		}
	cout<<arr[k-1]<<endl;
	}
}
```

可以举几个数字组合来找其中的规律：

例如：

k = 1 时，5 + 2 == 5 | 2 

k = 2 时，5 + 8 == 5 | 8 

k = 3 时，5 + 10  == 5 | 10 

k = 4 时，5 + 16  == 5 | 16 

k = 5 时，5 + 18  == 5 | 18 

...

![](/images/posts/Cpoint/4.png)

![](/images/posts/Cpoint/5.png)

满足这个运算规律 x + y == x | y 的二进制有：

0 + 0 == 0 | 0

1 + 0 == 1 | 0

1 + 1 !=  1 | 0 (只有这个不满足)

所以 x y 各自相对应的二进制位不能同时为 1，换言之， x 中 当前位 为 1 时， 与之对应的 y 那一位 肯定是0所以 x 位为 1 的就确定了，可以去除1。

![](/images/posts/Cpoint/6.png)

这正是 从 1 2 3 4 5 6 7，由于 y 表是按照 k 从1递增的顺序得到的值。所以你有理由猜想 这组新数据正是 k ！

程序使用的是入栈方法：

```cpp
0 1 0 1                入栈顺序：  0 0 1 0
0 0 0 1 不可以                    1 0 0 0
0 0 1 0 可以                      1 0 1 0   (要和前面的数据做或)
0 1 0 0 不可以                  1 0 0 0 0
1 0 0 0 可以                    1 0 0 1 0
1 0 0 0 0 可以                  1 1 0 0 0
                               1 1 0 1 0
```