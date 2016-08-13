---
layout: post
title: 函数模板和类模板
categories: C和C++基础
description: 函数模板和类模板
keywords: 函数模板和类模板
---

#### 函数模板

**函数模板可以用来创建一个通用的函数，以支持多种不同的形参，避免重载函数的函数体重复设计。它的最大特点是把函数使用的数据类型作为参数**。

函数模板提供一个种用来自动生成各种类型函数实例的算法，程序员对于函数接口参数和返回类型中的全部或者部分类型进行参数化(parameterize)而函数体保持不变。

下面是min()的函数模板定义：

```cpp
template<typename Type>
Type min(Type a,Type b){
	return a < b ? a : b;
}
```

```cpp
#include<iostream>

using namespace std;

template<typename T>  //template<class T>

T n_add(T a,T b){
	T c;
 	c=a+b;
 	return c;
} 

int main(){
 	cout<<n_add(5,3)<<endl;
 	cout<<n_add(5.35,5.5)<<endl;
 	system("pause");
}
```

**函数模板的重点是模板。表示的是一个模板，专门用来生产函数**。

**模板函数的重点是函数。表示的是由一个模板生成而来的函数**。

#### 类模板

![](/images/posts/C++/217.png)