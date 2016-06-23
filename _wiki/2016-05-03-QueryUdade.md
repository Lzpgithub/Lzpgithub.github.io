---
layout: post
title: 最高分是多少(华为)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

老师想知道从某某同学当中，分数最高的是多少，现在请你编程模拟老师的询问。当然，老师有时候需要更新某位同学的成绩. 

#### 输入描述

输入包括多组测试数据。每组输入第一行是两个正整数N和M**（0 < N <= 30000,0 < M < 5000）**,分别代表学生的数目和操作的数目。学生ID编号从1编到N。第二行包含N个整数，代表这N个学生的初始成绩，其中第i个数代表ID为i的学生的成绩接下来又M行，每一行有一个字符C（只取‘Q’或‘U’），和两个正整数A,B,当C为'Q'的时候, 表示这是一条询问操作，他询问ID从A到B（包括A,B）的学生当中，成绩最高的是多少当C为‘U’的时候，表示这是一条更新操作，要求把ID为A的学生的成绩更改为B。

#### 输出描述

对于每一次询问操作，在一行里面输出最高成绩.

#### 输入例子:

5 7

1 2 3 4 5

Q 1 5

U 3 6

Q 3 4

Q 4 5

U 4 5

U 2 9

Q 1 5

输出例子:

5

6

5

9

#### 程序代码


```cpp
#include <iostream>
#include <vector>

using namespace std;

struct inputOperate{
	char c;
	int A;
	int B;
};

int QueryOperate(vector<int> &grade,int A,int B){
	int max=0;
	if(A>B){
		int t=A;
		A=B;
		B=t;
	}
	for(int i=A-1;i<=B-1;i++){
		if(max<grade[i])
			max=grade[i];
	}
	return max;
}

void UpdateOperate(vector<int> &grade,int A,int B){
	grade[A-1]=B;
}

int main(){
	int stuNum,operateNum;
	cin>>stuNum>>operateNum;      //输入学生人数和操作数
	vector<int> grade;
	for(int i=0;i<stuNum;i++){    //输入学生成绩
		int num;
		cin>>num;
		grade.push_back(num);
	}
	vector<inputOperate> cmd;
	for(int i=0;i<operateNum;i++){
		inputOperate ino;
		cin>>ino.c>>ino.A>>ino.B;
		cmd.push_back(ino);
	}

	for(int i=0;i<operateNum;i++){
		if(cmd[i].c=='Q'){
			int max=QueryOperate(grade,cmd[i].A,cmd[i].B);
			cout<<max<<endl;
		}else{
			UpdateOperate(grade,cmd[i].A,cmd[i].B);
		}
	}

	system("pause");
}
```

