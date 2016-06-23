---
layout: post
title: 简单错误记录(华为)
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 题目描述

开发一个简单错误记录功能小模块，能够记录出错的代码所在的文件名称和行号。 

处理:

1.记录最多8条错误记录，对相同的错误记录(即文件名称和行号完全匹配)只记录一条，错误计数增加；(文件所在的目录不同，文件名和行号相同也要合并)

2.超过16个字符的文件名称，只记录文件的最后有效16个字符；(如果文件名不同，而只是文件名的后16个字符和行号相同，也不要合并)

3.输入的文件可能带路径，记录文件名称不能带路径

#### 输入描述

一行或多行字符串。每行包括带路径文件名称，行号，以空格隔开。

文件路径为windows格式

如：E:\V1R2\product\fpgadrive.c 1325

#### 输出描述

将所有的记录统计并将结果输出，格式：文件名代码行数数目，一个空格隔开，如: fpgadrive.c 1325 1 

结果根据数目从多到少排序，数目相同的情况下，按照输入第一次出现顺序排序。

如果超过8条记录，则只输出前8条记录.

如果文件名的长度超过16个字符，则只输出后16个字符

#### 输入例子:

E:\V1R2\product\fpgadrive.c 1325

输出例子:

fpgadrive.c 1325 1

#### 程序代码


```cpp
#include <iostream>
#include <vector>
#include <string>


using namespace std;

struct InputInfo{
	string filePathName;
	string errorLine;
	int errorNum;    //错误数目
	int n;           //出现顺序标号
};

void StoreData(vector<InputInfo> &InputData,string filepathname,string errorline){
	for(int i=0;i<InputData.size();i++){  //判断是否有重复
		if(InputData[i].filePathName.compare(filepathname)==0&&InputData[i].errorLine.compare(errorline)==0){
			InputData[i].errorNum++;      //错误计数增加
			return;
		}
	}
	InputInfo data;                       //如果没有重复 插入新的条目
	data.filePathName=filepathname;
	data.errorLine=errorline;
	data.errorNum=1;
	data.n=InputData.size();
	InputData.push_back(data);
}

void swapdata(InputInfo &data1,InputInfo &data2){
	InputInfo temp;
	temp.filePathName=data1.filePathName;
	temp.errorLine=data1.errorLine;
	temp.errorNum=data1.errorNum;
	temp.n=data1.n;

	data1.filePathName=data2.filePathName;
	data1.errorLine=data2.errorLine;
	data1.errorNum=data2.errorNum;
	data1.n=data2.n;

	data2.filePathName=temp.filePathName;
	data2.errorLine=temp.errorLine;
	data2.errorNum=temp.errorNum;
	data2.n=temp.n;
}

void SortData(vector<InputInfo> &InputData){
	for(int i=0;i<InputData.size()-1;i++){
		for(int j=0;j<InputData.size()-1-i;j++){
			if(InputData[j].errorNum<InputData[j+1].errorNum){
				swapdata(InputData[j],InputData[j+1]);
			}else if(InputData[j].errorNum==InputData[j+1].errorNum){
				if(InputData[j].n>InputData[j+1].n){
					swapdata(InputData[j],InputData[j+1]);
				}
			}else{}
		}
	}
}

string CutName(string strname){
	int len=strname.length();
	if(len>16){
		string name;
		for(int i=len-16;i<len;i++){
			name=name+strname[i];
		}
		return name;
	}else{
		return strname;
	}
}

int main(){
	vector<InputInfo> InputData;
	while(true){
		string str;               //输入一行字符串数据
		getline(cin,str);
		int strlen=str.size();
		if(strlen==0){            //如果输入字符串数据长度为0 跳出循环
			break;
		}

		int cut=strlen-1;
		while(str[cut]!=' '){     //定位空格位置
			cut--;
		}

		string errorline;
		for(int i=cut+1;i<strlen;i++)       //分割行号
			errorline=errorline+str[i];
		
		while(str[cut]!='\\'){              //定位符号'\'
			cut--;
		}

		string filepathname;
		cut++;
		while(str[cut]!=' '){               //分割文件名
			filepathname=filepathname+str[cut];
			cut++;
		}

		StoreData(InputData,filepathname,errorline);  //存储数据
	}

	SortData(InputData);

	for(int i=0;i<8;i++){
		cout<<CutName(InputData[i].filePathName)<<" "<<InputData[i].errorLine<<" "<<InputData[i].errorNum<<endl;
	}

	system("pause");
}
```

