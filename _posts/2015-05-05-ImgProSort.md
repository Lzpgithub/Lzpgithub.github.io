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

然后在对话框中创建菜单栏，菜单栏ID为IDR_MENU，在菜单栏中添加“打开”按钮。给菜单添加事件处理函数，函数名为OnOpenFile()。在函数中添加如下代码：

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

在菜单栏中添加“保存”按钮。给菜单添加事件处理函数，函数名为OnSaveFile()。在函数中添加如下代码：

```cpp
void CImgProSorltDlg::OnSaveFile()
{
	// TODO: 在此添加命令处理程序代码
	CString filter = _T("图像 (*.bmp;*.tif;*.jpg)|*.bmp;*.tif;*.jpg|All Files (*.*)|*.*||");
	CFileDialog fdlg(false,NULL,NULL,OFN_HIDEREADONLY |OFN_OVERWRITEPROMPT,filter);           //文件对话框
	fdlg.m_ofn.lpstrInitialDir = L"d:\\";                                                     //设置初始目录文件
	if(fdlg.DoModal() == IDOK)
	{
		CString fileName = fdlg.GetPathName();//得到保存文件的路径
		LPCTSTR lpszPathName;
		lpszPathName=fileName;           //文件路径CString由转换为LPCTSTR
		m_dib->Write(lpszPathName);
	}
}
```

如此软件就具备了打开图像和保存图像的功能，运行效果如下(软件的主窗口)：

![](/images/posts/Projection/1.png)



#### 图像的几何变换

这一节主要是给软件添加给图片进行几何变换的功能，在主窗口上添加“几何变换”菜单列。编写类GeometryTrans，负责对位图图像进行几何变换处理，该类集成在文件GeometryTrans.cpp和GeometryTrans.h中，因此需要在ImgProSorltDlg.cpp头文件中添加：#include "GeometryTrans.h"。主要添加功能包括：平移、水平镜像、垂直镜像、缩放、旋转(顺时针旋转90度，逆时针旋转90度，旋转180度，任意角度旋转)。在几何变换菜单列中添加相关菜单和事件处理函数。

首先是添加“平移”菜单，然后添加事件处理函数OnMove()。平移操作需要获取水平移动距离和垂直移动距离，我设计在点击平移菜单以后，弹出一个处理框来获取这两个参数，对话框ID为IDD_DLG_MOVE，同时要给对话框添加类GeometryMoveDlg，基类为CDialog。

![](/images/posts/Projection/2.png)

添加水平镜像菜单和事件处理函数OnHorizontalMirror()：

![](/images/posts/Projection/3.png)

添加垂直镜像菜单和事件处理函数OnHorizontalMirror()：

![](/images/posts/Projection/4.png)

添加缩放菜单和事件处理函数OnZoom()，同时需要添加对话框获取水平缩放参数和垂直缩放参数，对话框ID为IDD_DIALOG_ZOOM。

![](/images/posts/Projection/5.png)

添加旋转菜单和各个时间处理函数OnClockwise90(),OnAnticlockwise90(),OnRotate180()OnFreeRotate()，在任意角度时，需要添加对话框获取角度参数，对话框ID为IDD_DIALOG_Rotate。

![](/images/posts/Projection/6.png)

至此，图像处理软件的几何变换部分的功能已经添加完成，运行效果如下：

![](/images/posts/Projection/7.png)


#### 图像的灰度变换
有几何变换以后，开始给软件添加灰度变换部分的功能，主要是图像二值化，图像直方图，直方图均衡化，灰度反转，阈值变换，窗口变换，分段线性拉伸等等。灰度变换处理类是GrayTrans，在文件GrayTrans.cpp和GrayTrans.h。

首先是二值化菜单事件处理函数OnBinary()。

![](/images/posts/Projection/8.png)

直方图菜单主要是弹出对话框IDD_DIALOG_Histogram，显示图像的直方图，对话框类为HistogramDrawDlg，通过类向导给对话框类添加虚函数OnInitDialog()和消息OnPaint()并添加相关代码，则运行结果如下：

![](/images/posts/Projection/13.png)

构造函数是在c++类创建时首先运行的函数，主要是针对C++类对象的成员变量的初始化，是内在的。而OnInitDialog是主要针对与类对象相关的windows窗体控件初始化问题，是外在的。可以看出，构造函数是在窗体创建之前进行的，而OnInitDialog是在窗体创建完成后进行的。OnCreate是对话框在被创建时的消息，这时候对话框还没有被显示在屏幕上。而且对话框中的控件都还没有被创建。而OnInitDialog()是对话框创建完成，即对话框上的控件也全部被创建后第一次激活显示在屏幕上产生的消息。在此时可以对话框中的控件进行初始化操作

添加直方图均衡化菜单OnHistgramAver()和反转菜单OnImageReverse()，添加阈值变换菜单OnImgThresh()也要弹出对话框IDD_DLG_THRESH：

![](/images/posts/Projection/14.png)

窗口变换菜单OnThresholdWindow()，弹出对话框IDD_Dlg_THRESHWD：

![](/images/posts/Projection/15.png)

软件运行效果如下：

![](/images/posts/Projection/16.png)


#### 图像空间域的增加

这部分功能主要包括：高斯噪声，椒盐噪声，领域平均，中值平均，掩模平滑，梯度锐化，拉普拉斯锐化。处理空间域的文件是ImageEnhance.h和ImageEnhance.cpp，类是ImageEnhance。运行效果如下：

![](/images/posts/Projection/17.png)


#### 图像形态学

本部分功能包括腐蚀，膨胀，开运算，闭运算，击中击不中细化，处理文件是Morphology.h和Morphology.cpp，类是Morphology，运行效果：

![](/images/posts/Projection/18.png)


#### 图像分割

本部分功能包括自适应阈值分割，Robert算子，Sobel算子，Prewitt算子，Laplacian算子，边界跟踪，区域增长，处理文件是ImgSegment.h和ImgSegment.cpp，类是ImgSegment，运行效果：

![](/images/posts/Projection/19.png)

