---
layout: post
title:  字符串压缩算法
categories: C和C++基础
description: 字符串压缩算法
keywords: C, 字符串压缩算法
---


#### 哈夫曼编码

#### 重复个数

给定字符串 s，要求将连续出现的字符压缩至字符和数量，并返回新的字符串。

比如：s = "aabccccaaa"，则压缩后的字符串为 s2 = "a2b1c4a3"。

#### 压缩成整数

应用中，经常需要将字符串压缩成一个整数，即字符串散列。比如下面这些问题：（摘自JULY的博客  http://blog.csdn.net/v_july_v/ ）

（1）搜索引擎会通过日志文件把用户每次检索使用的所有检索串都记录下来，每个查询串的长度为1-255字节。请找出最热门的10个检索串。

（2）有一个1G大小的一个文件，里面每一行是一个词，词的大小不超过16字节，内存限制大小是1M。返回频数最高的100个词。

（3）有10个文件，每个文件1G，每个文件的每一行存放的都是用户的query，每个文件的query都可能重复。要求你按照query的频度排序。

（4）给定a、b两个文件，各存放50亿个url，每个url各占64字节，内存限制是4G，让你找出a、b文件共同的url。

（5）一个文本文件，大约有一万行，每行一个词，要求统计出其中最频繁出现的前10个词。

这些问题都需要将字符串压缩成一个整数，或者说是散列到某个整数 M 。然后再进行取余操作，比如 M%16，就可以将该字符串放到编号为M%16的文件中，相同的字符串肯定是在同一个文件中。通过这种处理，就可以将一个大文件等价划分成若干小文件，而对于小文件，就可以用常规的方法处理，内排序、hash_map等等。最后将这些小文件的处理结果综合起来，就可以求得原问题的解。

下面介绍一些字符串压缩的算法。

方法1：最简单就是将所有字符加起来，代码如下：

```cpp
unsigned long HashString(const char *pString, unsigned long tableSize)
{
	unsigned long hashValue = 0;
	while(*pString)
    	hashValue += *pString++;
	return hashValue % tableSize;
}
```

分析：如果字符串的长度有限，而散列表比较大的话，浪费比较大。例如，如果字符串最长为16字节，那么用到的仅仅是散列表的前16*127=2032。假如散列表含2729项，那么2032以后的项都用不到。

方法2：将上次计算出来的hash值左移5位（乘以32），再和当前关键字相加，能得到较好的均匀分布的效果。

```cpp
unsigned long HashString(const char *pString,unsigned long tableSize)
{
	unsigned long hashValue = 0;
	while (*pString)
		hashValue = (hashValue << 5) + *pString++;
	return hashValue % tableSize;
}
```

分析：这种方法需要遍历整个字符串，如果字符串比较大，效率比较低。
