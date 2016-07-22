---
layout: post
title: 位置转换与位运算
categories: C和C++基础
description: 编程基础
keywords: 位运算
---

C语言中的不同类型变量需要不同的字节长度进行存储，如果存在不同类型变量进行相互赋值时，就会发生位制转换，例如当char与int之间的相互转换时，由于char类型变量所占的内存小于int类型，因此不会发生数据丢失，而当int类型转换为char类型时，会发生数据字节丢失。

##### 1.分析代码写出结果--位制转换

```cpp
#include <iostream>

using namespace std;

int main(){
	int i=5.01;
	float f=5;
	printf("%f\n",5);       //输出结果不可预测  #1
	printf("%1f\n",5.01);   //输出5.0100       #2
	printf("%f\n",f);       //输出5.0000       #3

	printf("%d\n",5.01);    //输出结果不可预测  #4
	printf("%d\n",i);       //5               #5

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/96.png)

程序解析：

在32位平台中，int类型和float类型都占4字节，double占8字节，以下讨论都是32位平台。

printf根据说明符“%f”，认为参数是double类型的参数（在printf函数中，float会自动转换成double），因此从栈中读8个字节。类似地当printf后的说明符为“%d”，会认为参数时int类型的参数，因此从栈中读4字节。

1号行中，参数5为int型，所以在栈中分配了4字节的内存存放参数5，然后printf从栈中读8字节。很显然，内存访问越界，会有不可预料的情况发生。

2号行和3号行中参数5.01和f分别是double类型和float类型（注意这里f在赋值时已经作了int到float的转化），而float和double都是浮点类型，其相互转化是相对安全的，因此结果都能正常输出。

4号行参数5.01占8字节，而printf只读4字节，同样会出现不可预料的情况。

5号行参数i是由5.01转换过来的，转换结果是i等于5，占4字节，因此这段代码输出为5。


##### 2.分析代码写出结果--位运算

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	unsigned short int i=0;
	unsigned char ii=255;
	int j=8,p,q;     //j=0000 0000 0000 0000 0000 0000 0000 1000

	p=j<<1;          //16 左移乘以2
	q=j>>1;          //4 右移除以2
	i=i-1;           //i=1111 1111 1111 1111=65535
	ii=ii+1;         //0 溢出

	printf("i=%d\n",i);
	printf("ii=%d\n",ii);
	printf("p=%d\n",p);
	printf("q=%d\n",q);

	system("pause");
}
```

程序运行结果：

![](/images/posts/C++/97.png)


##### 3.设置或清除特定的位

嵌入式系统要求用户对变量或寄存器进行位操作。给定一个整型变量a，写两段代码，第1个设置a的BIT3，第2个清除a的BIT3。在以上两个操作中，要保持其他位不变。

```cpp
#define BIT3 (0x1<<3)
static int a;

void set_bit3(void){
	a|=BIT3;
}

void clear_bit3(void){
	a&=~BIT3;
}
```

##### 4.计算一字节里有多少位被置1

```cpp
#include <stdio.h>
#include <stdlib.h>

#define BIT7 (0x1<<7)

int calculate(unsigned char c){
	int count=0;
	int i=0;
	unsigned char comp=BIT7;

	for(i=0;i<sizeof(c)*8;i++){
		if((c&comp)!=0)          //与最高位相与
			count++;             //计算加1
		comp=comp>>1;            //每计算一位 向左移动一位
	}
	return count;
}

int main(){
	unsigned char c=97;          //1100001
	int count=0;
	count=calculate(c);
	printf("count=%d\n",count);  //输出为3
	system("pause");
}
```

##### 5.位运算改错

```cpp
//函数将相应的位置为0
#define BIT_MASK(bit_pos) (0x01<<(bit_pos))

int Bit_Reset(unsigned int *val,unsigned char pos){
	if(pos>=sizeof(unsigned int)*8)
		return 0;
	*val=(*val&& ~BIT_MASK(pos));    //出错 要使用位与&
	return 1;
}
```

##### 6.运用位运算交换a、b两个数

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){
	int a=3;
	int b=5;

	printf("a=%d, b=%d\n",a,b);  // 3  5

	//进行三次异或操作
	a^=b;
	b^=a;
	a^=b;

	printf("a=%d, b=%d\n",a,b);  // 5  3
	system("pause");
}
```

##### 7.列举并解释C++中四种运算符转化，说明它们的不同点

解析：

1.const_cast操作符：即供程序设计师在特殊情况下将限制为const成员函数的const定义解除，使其可以更改特定属性。

2.dynamic_cast操作符：如果启动了支持运行时间类型信息（RTTI），dynamic_cast有助于判断在运行时所指向对象的确定类型。它与typeid运算符有关，可以将一个基类的指针指向不同的子类型（派生类），然后将被转型作为基础类的对象还原成原来的类，不过，仅限于对象指针的类型转换。

3.reinterpret_cast操作符：将一个指针转换成其他类型的指针，新类型的指针与旧指针可以毫不相干。通常用于非标准的指针数据类型转换，例如将void\*转换为char\*。它也可以用于指针和整型数之间的类型转换。注意：它存在潜在的危险，除非有使用它的充分理由，否则就不要使用它。例如将一个int\*类型的指针转换为float\*类型的指针，很容易造成整数数据不能被正确地读取。

4.static_cast操作符：在相关的对象和指针类型之间进行类型转换。有关的类之间必须通过继承构造函数或者转换函数进行联系。static_cast操作符还能在数字类型之间进行类型转换。通常情况下，static_cast操作符用在将数域较大的类型转换为较小类型。当转换的类型时原始数据类型时，这种操作可以有效地禁止编译器发出警告。






