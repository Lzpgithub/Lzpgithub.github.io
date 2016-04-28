---
layout: post
title: 基于MFC的图像处理软件开发
categories: 个人项目
description: 基于MFC的图像处理软件开发
keywords:  图像处理, 软件, MFC
---

本项目是在MFC平台上编写C++代码来开发一个集成各种图像处理算法的软件，主要对位图图像进行处理。

##### 基于对话框打开读入图片

首先是创建一个基于对话框的应用程序，添加图像控件进行显示图像，图像控件ID为IDC_PICTURE。在工程中创建对位图处理的基本文件，里面声明类ImageDib，集成了对位图结构定义和基本操作，包括图像数据、图像颜色表	、图像的宽(像素为单位)、图像的高(像素为单位)、图像信息头等等。

```cpp
//ImageDib.h: interface for the ImageDib class.
class ImageDib  
{
	//成员变量
	public:	
		unsigned char * m_pImgData; 				//图像数据指针
		LPRGBQUAD m_lpColorTable; 				    //图像颜色表指针
		int m_nBitCount;						    //每像素占的位数
	private:
		LPBYTE m_lpDib;						    //指向DIB的指针
		HPALETTE m_hPalette;					//逻辑调色板句柄
		int m_nColorTableLength; 			    //颜色表长度（多少个表项）
	public:
		int m_imgWidth;							//图像的宽，像素为单位
		int m_imgHeight; 						//图像的高，像素为单位
		LPBITMAPINFOHEADER m_lpBmpInfoHead; 	//图像信息头指针
	//成员函数
	public:
		ImageDib();							    //构造函数
		ImageDib(CSize size, int nBitCount, LPRGBQUAD lpColorTable, 
		unsigned char *pImgData);               //带参数的构造函数
		~ImageDib();						    //析构函数
		BOOL Read(LPCTSTR lpszPathName); 		//DIB读函数
		BOOL Write(LPCTSTR lpszPathName);       //DIB写函数
		int ComputeColorTabalLength(int nBitCount);	    //计算颜色表的长度
		BOOL Draw(CDC* pDC, CPoint origin, CSize size); //图像绘制
		CSize GetDimensions();					        //读取图像维数
		void ReplaceDib(CSize size, int nBitCount, LPRGBQUAD lpColorTable,
		unsigned char *pImgData);              	//用新的数据替换DIB
	private:
		void MakePalette();						//创建逻辑调色板
		void Empty();                         	//清理空间
};
```

在ImgProSorltDlg.h头文件中添加：#include "ImageDib.h"，同时在类CImgProSorltDlg中声明ImageDib对象m_dib，以后在类CImgProSorltDlg中读入的位图数据就存到对象m_dib中，因此还需要在类CImgProSorltDlg的构造函数中进行初始化：m_dib=new ImageDib();

然后在对话框中创建菜单栏，在菜单栏中添加“打开”按钮。菜单ID为IDR_MENU，给菜单添加事件处理函数，函数名为OnOpenFile()。在函数中添加如下代码：

```cpp
void CImgProSorltDlg::OnOpenFile()
{
	// TODO: 在此添加命令处理程序代码
	CString FilePathName;      //存储文件的路径
    CFileDialog dlg(TRUE,      //TRUE为OPEN对话框，FALSE为SAVE AS对话框
        NULL, 
        NULL,
        OFN_HIDEREADONLY | OFN_OVERWRITEPROMPT,
        (LPCTSTR)_TEXT("JPG Files (*.bmp)|*.bmp|All Files (*.*)|*.*||"),
        NULL);
    if(dlg.DoModal()==IDOK)
    {
        FilePathName=dlg.GetPathName();      //文件名保存在了FilePathName里
		LPCTSTR lpszPathName;
		lpszPathName=FilePathName;           //文件路径CString由转换为LPCTSTR
		BOOL B=m_dib->Read(lpszPathName);

		CDC *pDc=GetDlgItem(IDC_PICTURE)->GetDC();       //获取DC
		CSize sizeFileDib;
		CRect bmpRC;
		GetDlgItem(IDC_PICTURE)->GetWindowRect(&bmpRC);  //获取图像控件的高度和宽度
		sizeFileDib.cx=bmpRC.Width();
		sizeFileDib.cy=bmpRC.Height();
		m_dib->Draw(pDc,CPoint(0,0),sizeFileDib);        //开始画图
    }
    else
    {
         return;
    }
}
```
运行效果如下：

![](/images/posts/Projection/1.png)
