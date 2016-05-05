---
layout: post
title: 基于MFC的条形码识别系统
categories: 个人项目
description: 基于MFC的条形码识别系统
keywords:  条形码, 软件, MFC
---

本项目是在MFC平台上编写C++代码来开发一个识别一维条形码的软件。

条形码(barcode)是将宽度不等的多个黑条和空白，按照一定的编码规则排列，用以表达一组信息的图形标识符。常见的条形码是由反射率相差很大的黑条（简称条）和白条（简称空）排成的平行线图案。条形码可以标出物品的生产国、制造厂家、商品名称、生产日期、图书分类号、邮件起止地点、类别、日期等许多信息，因而在商品流通、图书管理、邮政管理、银行系统等许多领域都得到广泛的应用。

其中使用最为广泛的是EAN-13码，本项目开发的识别系统就是以EAN-13码为例。

##### 条形码EAN-13的结构和编码规则

EAN-13条形码由13位数字构成，其中第一位是前置码，最后一位是校验码。条形码格式如下：

![](/images/posts/Projection/20.png)

条形码以模块为单位，其中“1”代表黑细线，“0”代表白细线，多个黑模块组成一个“条”，多个白模块组成一个“空”。

1.左侧空白区：位于条码符号最左侧与空的反射率相同的区域，其最小宽度为11个模块宽。

2.起始符：位于条码符号左侧空白区的右侧，表示信息开始的特殊符号，由3个模块组成。

3.左侧数据符：位于起始符右侧，表示6位数字信息的一组条码字符，由42个模块组成。

4.中间分隔符：位于左侧数据符的右侧，是平分条码字符的特殊符号，由5个模块组成。

5.右侧数据符：位于中间分隔符右侧，表示5位数字信息的一组条码字符，由35个模块组成。

6.校验符：位于右侧数据符的右侧，表示校验码的条码字符，由7个模块组成。

7.终止符：位于条码符号校验符的右侧，表示信息结束的特殊符号，由3个模块组成。

8.右侧空白区：位于条码符号最右侧的与空的反射率相同的区域，其最小宽度为7个模块宽。

9.供人识读字符：位于条码符号的下方，是与条码字符相对应的供人识别的13位数字，最左边一位称前置码。供人识别字符优先选用OCR-B字符集，字符顶部和条码底部的最小距离为0.5个模块宽。标准版商品条码中的前置码印制在条码符号起始符的左侧。

由此看出，条形码主要构成部分是2-7，起始符、中间分隔符、终止符属于特殊符，起始符和终止符都是101，由三个模块组成，三个条和空。中间分隔符由5个模块组成01010，5个条和空。

![](/images/posts/Projection/21.png)

左侧数据符、右侧数据符、校验符中每7个模块表示一个字符，从0到9。每一条码数据字符由2个条和2个空交替构成，每一条或空由1～4个模块组成，每一条码字符的总模块数为7。用二进制“1”表示黑的模块，用二进制“0”表示白的模块。

![](/images/posts/Projection/22.png)

因此一个EAN-13条形码一共有：3+6x4+5+5x4+1x4+3=59个条和空

有：3+12x7+5+3=95个模块。

条码的二进制表示方法有三个子集：A、B和C，左侧数据用A或者B表示，至于用A还是B由第一位系统码决定，右侧数据用C表示。

![](/images/posts/Projection/23.png)

下表中列出了左侧数据符的字符集的选择规则：

![](/images/posts/Projection/24.png)


##### 译码过程--平均值法

所谓的平均值法，就是对条形码符号图像中从起始符号到终止符号整个宽度进行测量，然后除以95，求出单位模块所含的像素列宽，即单位宽度，再分别测量各个条和空的实际宽度，最后根据宽度识别出一维条形码图像中所包含的信息。EAN-13是一种(7,2)码，即每个字符的总宽度为7个模块宽，交替由两个条和两个空组成。

![](/images/posts/Projection/25.png)

如上图所示为EAN-13条形码的一个字符，途中条、空宽度定义如下：C1，C2，C3和C4表示每个字符中4个相邻条、空的宽度，T是一个字符的宽度。设一个字符中单位模块的宽度为n，则单位模块宽度为：

n=T/7;

T=C1+C2+C3+C4;

由于条形码宽度C1，C2，C3，C4已知，设条形码条、空分别占单位模块的个数为mi,则：

mi=Ci/n   i=1,2,3,4;

由mi可知条形码的编号，例如m1=1,m2=3,m3=1,m4=2，且条形码的排列为条-空-条-空，则可知条形码编号为1000100.


##### 系统的流程与总体结构

一维条形码识别系统包括两个模块，即图像预处理模块和译码模块，如图所示：

![](/images/posts/Projection/26.png)


##### 系统的实现

本系统是基于MFC平台来实现，创建基于对话框程序，设计如图所示的界面，首先是两个图像控件，“打开文件”按钮用来打开读取图片并显示在原图图像控件中，点击“二值化图像”按钮进行二值化图像，点击“识别”按钮把识别结果显示出来。创建CBarRecog类来进行各种处理。

![](/images/posts/Projection/27.png)

二值化图像程序代码：

```cpp
/******************************************************************************
 * 功能：二值化图像
******************************************************************************/
void CBarRecog::BinaryImage(BYTE threhold)
{
	//未装载图像时不进行任何操作
	if(ImageArray == NULL)
		return;

	int i, j;
	for(i=0; i<ImageHeight; i++)
	{
		for(j=0; j<ImageWidth; j++)
		{
			if(ImageArray[i][j] >= threhold)
				ImageArray[i][j] = (BYTE)0;
			else 
				ImageArray[i][j] = (BYTE)1;
		}
	}
}
```

平均值法算法流程：

1.读取图像

2.进行水平方向和垂直方向上的直方图统计

3.找出包含条形码的区域

4.搜索宽度

5.根据宽度进行条形码的识别

求出条形码各个宽度的程序代码：

```cpp
/******************************************************************************
 * 功能：对条形码图像进行预处理，得出宽度序列
******************************************************************************/
BOOL CBarRecog::PreProcess()
{
	//阈值设为128
    BinaryImage(160);                  //二值化图像
	int i, j;
	int tempMax;
	int tempArray[1000];

	//进行水平方向和垂直方向上的直方图统计
	for(i=0; i<ImageHeight; i++)       //初始化arPixelV和arPixelH数组为0
		arPixelV[i] = 0;
	for(i=0; i<ImageWidth; i++)
		arPixelH[i] = 0;

	for(i=0; i<ImageHeight; i++)       //开始直方图统计
	{
		for(j=0; j<ImageWidth; j++)
		{
			if(ImageArray[i][j] == 1)
			{
				arPixelV[i] += 1;
				arPixelH[j] += 1;
			}
		}
	}

	//寻找包含条形码的区域，
	//线寻找水平方向上黑象素最大的行
	
	tempMax = 0;
	for(i=0; i<ImageHeight; i++)
	{
		if(arPixelV[i]>tempMax)
			tempMax = arPixelV[i];
		arMark[i] = false;
	}
	
	for(i=0; i<ImageHeight-1; i++)
	{
		//计算差分
		arDifference[i] = arPixelV[i+1] - arPixelV[i];

		//如果该行像素足够多且变化不大，标记为true
		if( (abs(arDifference[i])<20) && (arPixelV[i]>(0.75*tempMax)) )
			arMark[i] = true;
	}
	
	//确定包含条码的行
	int iLengthThrehold = 40;
	int iCount;
	for(i=0; i<ImageHeight-iLengthThrehold; i++)
	{
		iCount = 0;
		for(j=0; j<iLengthThrehold; j++)
		{
			if(arMark[i+j] == true)
				iCount++;
		}
		if(iCount >= 37)
		{
			ImageTop = i+10;		//确定顶部
			break;
		}
	}

	for(i=ImageHeight-1; i>=iLengthThrehold-1; i--)
	{
		iCount = 0;
		for(j=0; j<iLengthThrehold; j++)
		{
			if(arMark[i-j] == true)
				iCount++;
		}
		if(iCount >= 37)	//iLengthThrehold-3
		{
			ImageBottom = i-10;		//确定底部
			break;
		}
	}
	
	//寻找左边缘,为了保证鲁棒性，在已经确定的上下边界内全局搜索
	for(i=ImageTop; i<=ImageBottom; i++)
	{
		for(j=20; j<ImageWidth; j++)
		{
			if( (ImageArray[i][j-1]==0) && (ImageArray[i][j]==1) )
			{
				arLeftEdge[i] = j;
				break;
			}
		}
	}
	
	//为消除噪声的干扰，下面确定准确的左边界
	tempMax = 0;
	int iMax = 0;
	for(i=ImageTop; i<=ImageBottom; i++)
	{
		if(arLeftEdge[i] > tempMax)
		{
			tempMax = arLeftEdge[i];
			iMax = i;
		}
	}
	
	//倾斜度不能大于1/10
	iCount = 0;
	for(i=ImageTop; i<=ImageBottom; i++)
	{
		if( abs(tempMax-arLeftEdge[i]) < abs(i-iMax)/6+1 )
		{
			iCount++;
		}
	}
	
	if( (iCount/(ImageBottom-ImageTop))<0.6 )
		return false;
	
	//调整起点
	for(i=iMax; i>ImageTop; i--)
	{
		if( abs(arLeftEdge[i]-arLeftEdge[i-1])>=2 )
		{
			if(ImageArray[i-1][arLeftEdge[i]]-ImageArray[i-1][arLeftEdge[i]-1] == 1)
				arLeftEdge[i-1] = arLeftEdge[i];
			else if(ImageArray[i-1][arLeftEdge[i]-1]-ImageArray[i-1][arLeftEdge[i]-2] == 1)
				arLeftEdge[i-1] = arLeftEdge[i]-1;
			else if(ImageArray[i-1][arLeftEdge[i]+1]-ImageArray[i-1][arLeftEdge[i]] == 1)
				arLeftEdge[i-1] = arLeftEdge[i]+1;
			else
				arLeftEdge[i-1] = arLeftEdge[i];
		}
	}
	
	for(i=iMax; i<ImageBottom; i++)
	{
		if(i == ImageBottom)
			break;
	
		if( abs(arLeftEdge[i]-arLeftEdge[i+1])>=2 )
		{
			if(ImageArray[i+1][arLeftEdge[i]]-ImageArray[i+1][arLeftEdge[i]-1] == 1)
				arLeftEdge[i+1] = arLeftEdge[i];
			else if(ImageArray[i+1][arLeftEdge[i]-1]-ImageArray[i+1][arLeftEdge[i]-2] == 1)
				arLeftEdge[i+1] = arLeftEdge[i]-1;
			else if(ImageArray[i+1][arLeftEdge[i]+1]-ImageArray[i+1][arLeftEdge[i]] == 1)
				arLeftEdge[i+1] = arLeftEdge[i]+1;
			else
				arLeftEdge[i+1] = arLeftEdge[i];
		}
	}
	
	int n;
	//搜索出所有的宽度
	for(n=0; n<29; n++)
	{
		//搜索条的右边缘
		for(i=ImageTop; i<=ImageBottom; i++)
		{
			for(j = arLeftEdge[i]+1; j<ImageWidth; j++)
			{
				if( (ImageArray[i][j-1]==1) && (ImageArray[i][j]==0) )
				{
					arLeftEdge1[i] = j;
					break;
				}
			}
			arDelta[i] = arLeftEdge1[i] - arLeftEdge[i];
		}
		
		//假定条和空的宽度最多为11
		//排序，可以认为最中间的5个宽度是平均宽度
		for(i=ImageTop; i<ImageBottom; i++)
			tempArray[i] = arDelta[i];
		
		for(i=ImageTop; i<ImageBottom; i++)
		{
			for(j=ImageBottom; j>i; j--)
			{
				int tempSwap;
				if(tempArray[j] < tempArray[j-1])
				{
					tempSwap = tempArray[j];
					tempArray[j] = tempArray[j-1];
					tempArray[j-1] = tempSwap;
				}
			}
		}
		
		if(tempArray[ImageTop+(ImageBottom-ImageTop)/2+2]-tempArray[ImageTop+(ImageBottom-ImageTop)/2-2]>1)
			return false;
		else
			arWidth[2*n] = tempArray[ImageTop+(ImageBottom-ImageTop)/2];
		
		//调整下一列边缘
		for(i=ImageTop; i<=ImageBottom; i++)
		{
			if(abs(arDelta[i] - arWidth[2*n])>2)
				arLeftEdge1[i] = arLeftEdge[i] + arWidth[2*n];
			arLeftEdge[i] = arLeftEdge1[i];
		}
		
		//搜索空的右边缘
		for(i=ImageTop; i<=ImageBottom; i++)
		{
			for(j = arLeftEdge[i]+1; j<ImageWidth; j++)
			{
				if( (ImageArray[i][j-1]==0) && (ImageArray[i][j]==1) )
				{
					arLeftEdge1[i] = j;
					break;
				}
			}
			arDelta[i] = arLeftEdge1[i] - arLeftEdge[i];
		}
		
		//假定条和空的宽度最多为11
		//排序，可以认为最中间的5个宽度是平均宽度
		for(i=ImageTop; i<ImageBottom; i++)
			tempArray[i] = arDelta[i];
		
		for(i=ImageTop; i<ImageBottom; i++)
		{
			for(j=ImageBottom; j>i; j--)
			{
				int tempSwap;
				if(tempArray[j] < tempArray[j-1])
				{
					tempSwap = tempArray[j];
					tempArray[j] = tempArray[j-1];
					tempArray[j-1] = tempSwap;
				}
			}
		}
		
		if(tempArray[ImageTop+(ImageBottom-ImageTop)/2+2]-tempArray[ImageTop+(ImageBottom-ImageTop)/2-2]>1)
			return false;
		else
			arWidth[2*n+1] = tempArray[ImageTop+(ImageBottom-ImageTop)/2];
		
		//调整下一列边缘
		for(i=ImageTop; i<=ImageBottom; i++)
		{
			if(abs(arDelta[i] - arWidth[2*n+1])>2)
				arLeftEdge1[i] = arLeftEdge[i] + arWidth[2*n+1];
			arLeftEdge[i] = arLeftEdge1[i];
		}
	}
	
	//搜索最后一个条的右边缘
	for(i=ImageTop; i<=ImageBottom; i++)
	{
		for(j = arLeftEdge[i]+1; j<ImageWidth; j++)
		{
			if( (ImageArray[i][j-1]==1) && (ImageArray[i][j]==0) )
			{
				arLeftEdge1[i] = j;
				break;
			}
		}
		arDelta[i] = arLeftEdge1[i] - arLeftEdge[i];
	}
	
	//假定条和空的宽度最多为11
	//排序，可以认为最中间的5个宽度是平均宽度
	for(i=ImageTop; i<ImageBottom; i++)
		tempArray[i] = arDelta[i];
	
	for(i=ImageTop; i<ImageBottom; i++)
	{
		for(j=ImageBottom; j>i; j--)
		{
			int tempSwap;
			if(tempArray[j] < tempArray[j-1])
			{
				tempSwap = tempArray[j];
				tempArray[j] = tempArray[j-1];
				tempArray[j-1] = tempSwap;
			}
		}
	}
	
	if(tempArray[ImageTop+(ImageBottom-ImageTop)/2+2]-tempArray[ImageTop+(ImageBottom-ImageTop)/2-2]>1)
		return false;
	else
		arWidth[2*n] = tempArray[ImageTop+(ImageBottom-ImageTop)/2];
	
	//调整下一列边缘
	for(i=ImageTop; i<=ImageBottom; i++)
	{
		if(abs(arDelta[i] - arWidth[2*n+1])>2)
			arLeftEdge1[i] = arLeftEdge[i] + tempArray[ImageTop+(ImageBottom-ImageTop)/2];
		arLeftEdge[i] = arLeftEdge1[i];
	}

	return true;
}

```

识别条形码的程序代码：

```cpp
/******************************************************************************
 * 识别
******************************************************************************/
BOOL CBarRecog::Recognize()
{
	//总共有7×12＋3×2＋5＝ 95个单位宽度
	//有4×12＋3×2＋5＝59个宽度，
	int i;
	int result[12];
	double mx = 0.0;	//平均宽度

	for(i=0; i<59; i++)
		mx += (double)arWidth[i];
	mx /= 95.0;

	//起始条文
	for(i=0; i<3; i++)
	{
		double dTemp = (double)arWidth[i]/mx;
		if( dTemp<0.6 || dTemp>1.4 )
			break;
	}
	//起始码不符合要求
	//if(i<3)
	//	return false;
	
	//识别前6个
	for(i=0; i<6; i++)
	{
		result[i] = JudgNum(arWidth[i*4+3], arWidth[i*4+4], arWidth[i*4+5], arWidth[i*4+6], mx);
	}
	//识别后6个
	for(i=6; i<12; i++)
	{
		result[i] = JudgNum(arWidth[i*4+8], arWidth[i*4+9], arWidth[i*4+10], arWidth[i*4+11], mx);
	}

	//判断码制
	if( result[0]==7 && result[1]==7 )
	{
		strCodeStyle = "ISSN";
	}
	else if( result[0]==7 && result[1]==8 )
	{
		strCodeStyle = "ISBN";
	}
	else
		strCodeStyle = "Unknown!";

	//判断是否全部识别出来
	for(i=0; i<12; i++)
		if(result[i] == -1)
			return false;

	CString strTemp;
	strCodeNumber.Format(L"");
	for(i=0; i<12; i++)
	{
		strTemp.Format(L"%d", result[i]);
		strCodeNumber += strTemp;
	}
	return true;
}
```

根据宽度数组判断数字的程序代码：

```cpp
int CBarRecog::JudgNum(int w1, int w2, int w3, int w4, double mx)
{
	double a1, a2, a3;
	int ia1, ia2, ia3;
	a1 = (double)(w1+w2)/mx;
	a2 = (double)(w2+w3)/mx;
	a3 = (double)(w3+w4)/mx;
	ia1 = (int)(a1+0.5);
	ia2 = (int)(a2+0.5);
	ia3 = (int)(a3+0.5);
		
	//判断该码值
	if( (ia1==5 && ia2==3 && ia3==2) || (ia1==2 && ia2==3 && ia3==5) )
		return 0;
	
	if( (ia1==4 && ia2==4 && ia3==3) || (ia1==3 && ia2==4 && ia3==4) )
	{
		if(ia1 == 4)
		{
			double dw2 = (double)w2/mx;
			if(dw2 < 2.4)
				return 1;
			else if(dw2 > 2.6)
				return 7;
			else return -1;
		}
	
		if(ia1 == 3)
		{
			double dw3 = (double)w3/mx;
			if(dw3 < 2.4)
				return 1;
			else if(dw3 > 2.6)
				return 7;
			else return -1;
		}
	}
	
	if( (ia1==3 && ia2==3 && ia3==4) || (ia1==4 && ia2==3 && ia3==3) )
	{
		if(ia1 == 3)
		{
			double dw4 = (double)w4/mx;
			if(dw4 < 2.4)
				return 2;
			else if(dw4 > 2.6)
				return 8;
			else return -1;
		}
	
		if(ia1 == 4)
		{
			double dw1 = (double)w1/mx;
			if(dw1 < 2.4)
				return 2;
			else if(dw1 > 2.6)
				return 8;
			else return -1;
		}
	}
	
	if( (ia1==5 && ia2==5 && ia3==2) || (ia1==2 && ia2==5 && ia3==5) )
		return 3;
	
	if( (ia1==2 && ia2==4 && ia3==5) || (ia1==5 && ia2==4 && ia3==2) )
		return 4;
	
	if( (ia1==3 && ia2==5 && ia3==4) || (ia1==4 && ia2==5 && ia3==3) )
		return 5;
	
	if( (ia1==2 && ia2==2 && ia3==5) || (ia1==5 && ia2==2 && ia3==2) )
		return 6;
	
	if( (ia1==4 && ia2==2 && ia3==3) || (ia1==3 && ia2==2 && ia3==4) )
		return 9;

	return false;
}

```

软件运行效果：

![](/images/posts/Projection/28.png)
