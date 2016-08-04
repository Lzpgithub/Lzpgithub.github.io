---
layout: post
title: 百度2017暑期实习生编程题
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

#### 单词接龙

拉姆刚开始学习英文单词，对单词排序很感兴趣。如果给拉姆一组单词，他能够迅速确定是否可以将这些单词排列在一个列表中，使得该列表中任何单词的首字母与前一单词的为字母相同。你能编写一个程序来帮助拉姆进行判断吗？

##### 输入描述:

  输入包含多组测试数据。

  对于每组测试数据，第一行为一个正整数n，代表有n个单词。

  然后有n个字符串，代表n个单词。

  保证：

  2<=n<=200,每个单词长度大于1且小于等于10,且所有单词都是由小写字母组成。

 
##### 输出描述:

对于每组数据，输出"Yes"或"No"

##### 输入例子:

  3

  abc

  cdefg

  ghijkl

  4

  abc

  cdef

  fghijk

  xyz
  

##### 输出例子:

  Yes

  No



```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main(){
	int n;
	while(cin>>n){
		vector<string> shuzu;             //输入字符串
		for(int i=0;i<n;i++){
			string strtemp;
			cin>>strtemp;
			shuzu.push_back(strtemp);
		}

		string headstr;
		string tailstr;
		headstr=shuzu[0];
		tailstr=shuzu[0];
		bool *m=(bool*)malloc(n*sizeof(bool));
		for(int i=0;i<n;i++)              //初始化标记
			m[i]=false;

		m[0]=true;
		for(int j=1;j<n;j++){
			for(int i=1;i<n;i++){
				if(m[i]==false){
					int slen=shuzu[i].length();
					if(shuzu[i][slen-1]==headstr[0]){    //插入开头
						headstr=shuzu[i];
						m[i]=true;
						break;
					}
					int tlen=tailstr.length();           //插入结尾
					if(shuzu[i][0]==tailstr[tlen-1]){
						tailstr=shuzu[i];
						m[i]=true;
						break;
					}
				}
			}
		}
		bool yorn=true;
		for(int i=0;i<n;i++){
			if(m[i]==false){
				yorn=false;
				break;
			}
		}

		if(yorn)
			cout<<"Yes"<<endl;
		else
			cout<<"No"<<endl;
		free(m);
	}
}
```


#### 进程调度算法

短作业优先（SJF, Shortest Job First）又称为“短进程优先”SPN(Shortest Process Next)；是对FCFS算法的改进，其目标是减少平均周转时间。

短作业优先调度算法基于这样一种思想：

运行时间短的优先调度；

如果运行时间相同则调度最先发起请求的进程。

PS:本题题面描述有误，但原题如此，不宜修改，实际优先级如下:

1)接到任务的时间；

2) 如果接收时间相同则调度 运行时间最短的任务。

等待时间：一个进程从发起请求到开始执行的时间间隔。

现在有n个进程请求cpu，每个进程用一个二元组表示：(p,q),p代表该进程发起请求的时间，p代表需要占用cpu的时间。

请计算n个进程的平均等待时间。


##### 输入描述:

输入包含多组测试数据。

对于每组测试数据，第一行为一个整数n。

然后有n行，每行两个整数，代表上述的二元组(p,q).

保证:2<=n<=2000,1<=p<=300,1<=q<=100.

##### 输出描述:

对于每组数据，输出一个浮点数，代表平均等待时间，请保留4位有效数字

##### 输入例子:

4

1 4

1 3

1 5

2 1
  
##### 输出例子:

5.2500

程序1：

```cpp
#include <iostream>
#include <vector>

using namespace std;

struct Progress{
	int Startime;
	int Occupytime;
};

int main(){
	int n;
	while(cin>>n){                      
		vector<Progress> pg;
		for(int i=0;i<n;i++){                     //输入数据
			Progress progr;
			cin>>progr.Startime>>progr.Occupytime;
			pg.push_back(progr);
		}

		for(int i=0;i<n-1;i++){                   //进行一次选择排序
			Progress temp;
			temp.Startime=pg[i].Startime;
			temp.Occupytime=pg[i].Occupytime;
			for(int j=i+1;j<n;j++){
				int pos=0;
				if(temp.Startime>pg[j].Startime){
					temp.Startime=pg[j].Startime;
					temp.Occupytime=pg[j].Occupytime;
					pos=j;
				}else if(temp.Startime==pg[j].Startime){
					if(temp.Occupytime>pg[j].Occupytime){
						temp.Startime=pg[j].Startime;
						temp.Occupytime=pg[j].Occupytime;
						pos=j;
					}
				}
				if(pos){                                      //交换数据
					pg[pos].Startime=pg[i].Startime;
					pg[pos].Occupytime=pg[i].Occupytime;
					pg[i].Startime=temp.Startime;
					pg[i].Occupytime=temp.Occupytime;
				}
			}
		}

		int *Thetime=(int*)malloc(n*sizeof(int));

		Thetime[0]=pg[0].Startime;                              
		float sumwaitime=0;                            //统计等待时间
		for(int i=1;i<n;i++){                          //排序以后第一个进程的等待时间肯定为0 第一个就不用计算了
			Thetime[i]=Thetime[i-1]+pg[i-1].Occupytime;
			if(Thetime[i]<pg[i].Startime)
				Thetime[i]=pg[i].Startime;
			sumwaitime=sumwaitime+Thetime[i]-pg[i].Startime;
		}
		printf("%.4f\n",sumwaitime/n);
	}
}
```

程序2：

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main(){
    int n;
    while (cin >> n){
        vector<int> rts;
        vector<int> cts;
        for (int i = 0; i < n; i++){
            int r, t;
            cin >> r >> t;
            rts.push_back(r);
            cts.push_back(t);
        }
 
        int cur_time = 1;
        double wait_time = 0;
 
        while (!rts.empty()){
            // 确定要执行进程
            int cur_process = 0;
            for (int i = 0; i < rts.size(); i++) {
                if (rts[i] < rts[cur_process] || (rts[i] == rts[cur_process] && cts[i] < cts[cur_process])) // 对应两个优先级
                    cur_process = i;
            }
            if (cur_time > rts[cur_process]){
                wait_time = wait_time + (cur_time - rts[cur_process]);
                cur_time = cur_time + cts[cur_process];
            }
            else{
                cur_time = rts[cur_process] + cts[cur_process];
            }
            rts.erase(rts.begin() + cur_process);
            cts.erase(cts.begin() + cur_process);
        }
 
        printf("%.4f\n", wait_time / n);
    }
}
```