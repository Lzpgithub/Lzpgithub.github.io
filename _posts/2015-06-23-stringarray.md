---
layout: post
title: 字符串与数组
categories: C和C++基础
description: 编程基础。
keywords: C++，基础
---

字符串一般是使用字符数组的方式存储，例如下面的str定义：

```cpp
char str[]="123456";
```

这里str是一个字符数组，它存放了一个字符串“123456”，由于字符串还有一个结束符“\0”，所以此数组的长度为7而不是6。

##### strcpy函数与memcpy函数

strcpy和memcpy都是标准C库函数：

1.strcpy提供了字符串的复制。即strcpy只用于字符串复制，并且它不仅复制字符串内容之外，还会复制字符串的结束符。

2.memcpy提供了一般内存的复制。即memcpy对于需要复制的内容没有限制，因此用途更广。


##### 编程实现strcpy函数

已知strcpy函数的原型是：

```cpp
char *strcpy(char *strDest,const char *strSrc);
```

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string>

char *Mstrcpy(char *strDest,const char *strSrc){    //实现strSrc到strDest的复制
	if((strDest==NULL)||(strSrc==NULL)){            //判断参数strDest和strSrc的有效性
		return NULL;
	}
	char *strDestCopy=strDest;                      //保存目标字符串的首地址
	while((*strDest++=*strSrc++)!='\0');            //把strSrc字符串的内容复制到strDest下

	return strDestCopy;
}

int getStrLen(const char *strSrc){           //实现获取strSrc字符串的长度
	int len=0;                               //保存长度
	while(*strSrc++!='\0'){                  //循环直到遇见结束符'\0'为止
		len++;
	}
	return len;
}

int main(){
	char strSrc[]="Hello World";            //要被复制的源字符串
	char strDest[20];                       //要复制到目的字符数组
	char strDest2[20];
	int len=0;                              //保存目的字符数组中字符串的长度

	len=getStrLen(Mstrcpy(strDest,strSrc)); //链式表达式 先复制后计算长度
	printf("strDest:%s\n",strDest);         
	printf("Length of strDest:%d\n",len);
	strcpy(strDest2,strSrc);
	printf("strDest2:%s\n",strDest2); 

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/71.png)


strcpy函数返回char*类型的原因是为了能使用链式表达式。首先调用strcpy使得strDest指针复制strSrc的内存数据，然后调用getStrLen函数获取strDest字符串的长度，这样不仅调用方便，而且程序结构简洁明了。


##### 6.编程实现memcpy函数

内存复制的实现。

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>

void *memcpy2(void *memTo,const void *memFrom,size_t size){
	assert((memTo!=NULL)&&(memFrom!=NULL));     //保存memTo和memFrom必须有效
	char *tempFrom=(char*)memFrom;              //保存memFrom的首地址
	char *tempTo=(char*)memTo;                  //保存memTo首地址

	while(size-->0)             //循环size次 复制memFrom的值到memTo中
		*tempTo++=*tempFrom++;
	return memTo;
}

int main(){
	char strSrc[]="Hello World!";    //将被复制的字符数组
	char strDest[20];                //目的字符数组
	memcpy2(strDest,strSrc,4);       //复制strSrc的前4个字符到strDest中
	strDest[4]='\0';                 //把strDest的第5个元素赋为结束符'\0'
	printf("strDest:%s\n",strDest);  

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/72.png)


##### 7.strcpy与memcpy的区别

1.复制内容不同。strcpy只能复制字符串，而memcpy可以复制任意内容，例如字符数组、整型、结构体、类等等。

2.复制的方法不同。strcpy不需要指定长度，它遇到字符串结束符“\0”便结束。memcpy则是根据其第三个参数决定复制的长度。

3.用途不同。通常在复制字符串时用strcpy，而需要复制其他类型数据时则一般用memcpy。


##### 8.改错--数组越界

```cpp
void test1(){
	char string[10];
	char *strl="0123456789";  //还有一个结束符“\0”
	strcpy(string,strl);
}
```

```cpp
void test2(){
	char string[10],strl[10];
	int i;
	for(i=0;i<10;i++){
		strl[i]='a';
	}
	strcpy(string strl);     //没有结束符“\0”的存储空间
}
```

```cpp
void test3(char *strl){
	char string[10];
	if(strlen(strl)<=10){    //应该改为9
		strcpy(string,strl);
	}
}
```

##### 9.分析程序--数组越界

```cpp
#define MAX 255

int main(){
	unsigned char A[MAX],i;
	for(i=0;i<=MAX;i++)      //<=越界
		A[i]=i;
}
```

##### 10.分析程序--打印操作可能产生数组越界

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	int a[5]={0,1,2,3,4},*p;
	p=a;
	printf("%d\n",*(p+4*sizeof(int)));  //越界 打印是随机数

	system("pause");
}
```

##### 11.编程实现字符串的长度检测

字符串是以“\0”作为结束符。

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>

int strlen1(const char *src){
	assert(src!=NULL);            //src必须有效
	int len=0;                    //保存src的长度
	while(*src++!='\0')           //遇到结束符'\0'退出循环
		len++;
	return len;
}

int strlen2(const char *src){
	assert(src!=NULL);            //src必须有效
	const char *temp=src;         //保存src首地址
	while(*src++!='\0');          //遇到结束符'\0'退出循环
	return (src-temp-1);          //返回尾部指针与头部指针之差即长度
}

int main(){
	char p[]="Hello World!";
	printf("strlen1 len:%d\n",strlen1(p));   //打印方法1得到的结果
	printf("strlen2 len:%d\n",strlen2(p));   //打印方法2得到的结果
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/73.png)

assert宏的原型定义在<assert.h>中，其作用是如果它的条件返回错误，则终止程序执行。strlen2函数明显比strlen1函数效率高。


##### 12.编程实现字符串中子串的查找

```cpp
#include <stdio.h>
#include <stdlib.h>

const char *strstr(const char *src,const char *sub){
	const char *bp;
	const char *sp;
	if(src==NULL||sub==NULL)   //判断src与sub的有效性
		return src;
	while(*src){               //遍历src字符串
		bp=src;                //用于src的遍历
		sp=sub;                //用于sub的遍历
		do{                    //遍历sub字符串
			if(!*sp)           //如果到了sub字符串结束符位置
				return src;    //表示找到了sub字符串 退出
		}while(*bp++==*sp++);
		src+=1;                //开始下一个字符位置
	}
	return NULL;
}

int main(){
	char *p="12345";
	char *q="34";
	const char *r=strstr(p,q);
	printf("r:%s\n",r);
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/74.png)


##### 13.编程实现字符串中个单词的翻转

编写函数，将“I am from Shanghai”倒置为“Shanghai from am I”，即将句子中单词位置倒置，而不改变单词内部的结构。

```cpp
#include <iostream>
using namespace std;

char *RevStr(char *src){
	char str1[20];
	int c=0;
	char *temp=src;
	while(*temp++!='\0')     //确定字符串的长度
		c++;
	for(int i=0;i<c;i++)     //整个字符串进行逆序
		str1[i]=src[c-i-1];

	static char str2[20];
	int x=0;
	for(int i=0;i<=c;i++){   //每个单词进行逆序
		if(str1[i]==' '||i==c){
			for(int j=x;j<i;j++){
				str2[j]=str1[x+i-j-1];
			}
			str2[i]=' ';
			x=i+1;
		}
	}
	str2[c]='\0';
	return str2;
}


int main(){
	char *src="I am from Shang Hai";
	char *r=RevStr(src);
	cout<<r<<endl;
	system("pause");
}
```

##### 14.编程判断字符串是否为回文

```cpp
#include <iostream>
using namespace std;

int IsRevStr(char *str){
	int i,len;
	int found=1;             //1表示是回文字符串 0表示不是

	if(str==NULL)            //判断str的有效性
		return -1;

	len=strlen(str);         //获取字符串长度

	for(i=0;i<len/2;i++){    //遍历中如果发现相应头尾字符不等
		if(*(str+i)!=*(str+len-i-1)){  //则字符串不是回文
			found=0;
			break;
		}
	}
	return found;
}

int main(){
	char str1[10]="1234321";       //回文字符串
	char str2[10]="1234221";       //非回文字符串
	int test1=IsRevStr(str1);      //测试str1是否为回文
	int test2=IsRevStr(str2);      //测试str2是否为回文

	cout<<"str1 is "<<(test1==1?"":"not")<<" reverse string"<<endl;
	cout<<"str2 is "<<(test2==1?"":"not")<<" reverse string"<<endl;

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/75.png)



##### 15.编程实现strcmp库函数

实现C/C++库函数中的strcmp函数。对于两个字符串str1和str2，若相等则返回0，若str1大于str2则返回1，若str1小于str2返回-1。

```cpp
#include <iostream>
using namespace std;

int mystrcmp(const char *src,const char *dst){
	int ret=0;
	//循环比较两个字符是否相等
	//如果不等或者到了dst字符串末尾 退出循环
	while(!(ret=*(unsigned char *)src-*(unsigned char *)dst)&&*dst){
		++src;
		++dst;
	}

	if(ret<0)           //ret保存着字符比较的结果
		ret=-1;
	else if(ret>0)
		ret=1;
	return ret;
}

int main(){
	char str[10]="1234567";
	char str1[10]="1234567";
	char str2[10]="12345678";
	char str3[10]="1234566";

	int test1=mystrcmp(str,str1);
	int test2=mystrcmp(str,str2);
	int test3=mystrcmp(str,str3);

	cout<<"test1="<<test1<<endl;
	cout<<"test2="<<test2<<endl;
	cout<<"test3="<<test3<<endl;

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/76.png)


##### 16.编程查找两个字符串的最大公共子串

对于两个字符串A和B，如果A=“aocdef”，B=“pmcdfa”，则输出“cdf”。

```cpp
#include <iostream>

using namespace std;

int main(){
	char *A="acdfvgdcoe";
	char *B="pcdfvbdcma";
	
	int la=strlen(A);
	int lb=strlen(B);
	int len;
	if(la>lb)
		len=lb;
	else
		len=la;

	int start=0;
	int c=0;
	int maxlen=0;
	for(int i=0;i<len;i++){
		if(A[i]==B[i]){
			c++;               //开始计数长度
		}else{
			if(maxlen<c){      //记录最大长度
				maxlen=c;
				start=i-c;
			}
			c=0;
		}
	}
	
	char str[10];
	for(int i=start;i<start+maxlen;i++){   //复制最大公共字符串
		str[i-start]=A[i];
	}
	str[maxlen]='\0';

	cout<<"A:"<<A<<endl;
	cout<<"B:"<<B<<endl;
	cout<<str<<endl;

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/77.png)


##### 17.不能用printf系列的库函数，将十进制数以二进制和十六进制的形式输出。

```cpp
#include <iostream>
#include <math.h>

using namespace std;

void get2String(int n){
	char arr[33];
	for(int i=0;i<32;i++)  //首先将数组初始化为0字符
		arr[i]=0+'0';
	arr[32]='\0';

	int c=31;
	while(n>0){            //转换为二进制并存储
		char num=n%2+'0';
		arr[c]=num;
		c--;
		n=n/2;
	}
	cout<<arr<<endl;
}

void get16String(int n){
	int arr[32];
	for(int i=0;i<32;i++)  //首先将数组初始化为0
		arr[i]=0;

	int c=31;
	while(n>0){            //转换为二进制并存储
		char num=n%2;
		arr[c]=num;
		c--;
		n=n/2;
	}

	char str[11];
	str[10]='\0';
	str[0]='0';
	str[1]='x';
	c=2;
	for(int i=0;i<32;i=i+4){
		int temp=arr[i]*pow(2.0,3) + arr[i+1]*pow(2.0,2)
			+arr[i+2]*pow(2.0,1)+arr[i+3];
		char chartemp;
		if(temp==10)
			chartemp='A';
		else if(temp==11)
			chartemp='B';
		else if(temp==12)
			chartemp='C';
		else if(temp==13)
			chartemp='D';
		else if(temp==14)
			chartemp='E';
		else if(temp==15)
			chartemp='F';
		else
			chartemp=temp+'0';
		str[c]=chartemp;
		c++;
	}
	cout<<str<<endl;
}

int main(){
	get2String(456789);
	get16String(456789);
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/78.png)


##### 18.在字符串中，插入字符统计的个数

需要在字符串中插入字符统计的个数。例如字符串aaab，插入字符个数后变成aaa3b1。

```cpp
#include <iostream>
#include <math.h>

using namespace std;


void InsertNum(char *str){
	char strnum[20];
	int c1=0;
	int c2=0;
	int len=0;
	char leter;
	while(str[c1]!='\0'){
		if(len==0){            //第一个字母开始统计
			leter=str[c1];
			len++;             //开始计数
		}else{
			if(leter==str[c1]){  //当前的字母和上一个字母相同
				len++;
			}else{               //如果不相同
				for(int i=c1-len;i<c1;i++){   //复制str到strnum
					strnum[c2]=str[i];
					c2++;
				}
				strnum[c2]=len + '0';           //在strnum中插入数字
				c2++;
				leter=str[c1];                  //重新开始计数
				len=1;                          //开始计数
			}
		}
		c1++;
	}
	strnum[c2]='\0';
	cout<<strnum<<endl;
}

int main(){
	char *str="aaabaadddfghh";
	InsertNum(str);
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/79.png)

##### 19.字符串编码例题

对于一个长度小于20的字符串进行编码，遵循3个规则：

1.字母后面的第4个字母替换。例如：a->e，A->E，x->b，y->c，z->d。

2.如果字符不是字母，字符保持不变。

3.翻转整个字符串。


```cpp
#include <iostream>

using namespace std;

void StringCode(char *str){
	int len=0;
	char *p=str;
	while(*p++!='\0')
		len++;

	char strL[20];
	int c=0;
	for(int i=len-1;i>=0;i--){
		char temp=str[i];
		if((temp>='a'&&temp<='z')||
			(temp>='A'&&temp<='Z')){
			temp=temp+4;
			if(temp>'z')
				temp='a'+temp-'z'-1;
			if(temp>'Z'&&temp<='a')
				temp='A'+temp-'Z'-1;
		}
		strL[c]=temp;
		c++;
	}
	strL[c]='\0';
	cout<<strL<<endl;
}


int main(){
	char *str="hasd11H";
	StringCode(str);
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/80.png)


##### 20.反转字符串，但其指定的子串不反转。

给定一个字符串以及该字符串的子串，将字符串反转，但子串部分不反转，如：

输入字符串为：“Welcome you,my friend”；

子串为：“you”

输出为：“dneirf ym,you emocleW”

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void reverse(const char *s1,const char *token){
	char str[50];                //先把s1复制到str
	strcpy(str,s1);

	const char *subStr;          //求子串的长度  
	subStr=token;
	int sublen=0;
	while(*subStr++!='\0')
		sublen++;

	int s1len=0;                 //求s1字符串
	const char *srcstr=s1;
	while(*srcstr++!='\0')
		s1len++;
	
	int start=0;                 //标定子串的位置
	for(int i=0;i<s1len;i++){
		if(s1[i]==token[0]){
			int j;
			for(j=i+1;j<i+sublen;j++){
				if(s1[j]!=token[j-i])
					break;
			}
			if(j==i+sublen){
				start=i;
				break;
			}
		}
	}

	int c=1;                     //反转子字符串
	for(int i=start;i<start+sublen;i++){
		str[i]=s1[start+sublen-c];
		c++;
	}

	char strLast[50];            //反转整个字符串
	strLast[s1len]='\0';
	for(int i=0;i<s1len;i++)
		strLast[i]=str[s1len-i-1];

	printf("%s\n",strLast);
}

int main(){
	char *s1="Welcome you,my friend";
	char *token="you";
	printf("%s\n",s1);
	reverse(s1,token);
	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/81.png)


##### 21.编写字符串反转函数strrev

编写字符串反转函数：strrev，要求时间和空间效率都尽量高

测试用例：输入“abcd”，输出为“dcba”。

解析：最简单的解法是遍历字符串，将第一个字符和最后一个字符交换，第二个和倒数第二个字符交换，依次循环。

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string>
#include <iostream>

using namespace std;

char *strrev1(const char *str){      //通过数组下标方式访问字符串
	int len=strlen(str);
	char *tmp=new char[len+1];
	strcpy(tmp,str);
	for(int i=0;i<len/2;i++){
		char c=tmp[i];
		tmp[i]=tmp[len-i-1];
		tmp[len-i-1]=c;
	}
	return tmp;
}

char *strrev2(const char *str){    //用指针直接操作
	char *tmp=new char[strlen(str)+1];
	strcpy(tmp,str);
	char *ret=tmp;                 //字符串开头
	char *p=tmp+strlen(str)-1;     //字符串结尾
	while(p>tmp){
		char t=*tmp;               //头和尾交换
		*tmp=*p;
		*p=t;

		--p;
		++tmp;
	}
	return ret;
}


char *strrev3(const char *str){    //借助异或运算符完成两个字符的交换
	char *tmp=new char[strlen(str)+1];
	strcpy(tmp,str);
	char *ret=tmp;                 //字符串开头
	char *p=tmp+strlen(str)-1;     //字符串结尾

	while(p>tmp){
		*p^=*tmp;
		*tmp^=*p;
		*p^=*tmp;
		--p;
		++tmp;
	}
	return ret;
}

char *strrev4(const char *str){   //相加和相减来完成字符的交换
	char *tmp=new char[strlen(str)+1];
	strcpy(tmp,str);
	char *ret=tmp;                 //字符串开头
	char *p=tmp+strlen(str)-1;     //字符串结尾

	while(p>tmp){
		*p=*p+*tmp;
		*tmp=*p-*tmp;
		*p=*p-*tmp;
		--p;
		++tmp;
	}
	return ret;
}


char *strrev5(char *str,int len){     //递归算法实现
	if(len<=1)
		return str;

	char t=*str;                      
	*str=*(str+len-1);                
	*(str+len-1)=t;

	return (strrev5(str+1,len-2)-1);  //字符串除去开头和结尾 减少两个字符 减1保持字符串开头地址不变
}

int main(){
	char str[]="123456";
	cout<<str<<endl;

	char *str1=strrev1(str);
	cout<<str1<<endl;

	char *str2=strrev2(str1);
	cout<<str2<<endl;

	char *str3=strrev3(str2);
	cout<<str3<<endl;

	char *str4=strrev4(str3);
	cout<<str4<<endl;

	char *str5=strrev5(str4,6);
	cout<<str5<<endl;

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/82.png)