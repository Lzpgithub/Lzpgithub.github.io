---
layout: post 
title: PictureBox控件应用
categories: MFC学习
description: PictureBox控件应用
keywords:  PictureBox, MFC
---

1.创建一个基于对话框的应用程序；

2.添加一个Picture Box控件和一个按钮控件：

3.给CPictureBoxDlg类添加变量：

![](/images/posts/MFC/30.png)

![](/images/posts/MFC/31.png)

4.修改CPictureBoxDlg类OnPaint()函数添加的代码要写在原有代码的上面：

```cpp
void CPictureBoxDlg::OnPaint()
{
///////////////////////////////////////添加代码
	if(m_strPath == "")
	{
		return;
	}
	CPaintDC dc(this);                                   //当前设备描述符
	HBITMAP bmpHandle = (HBITMAP)LoadImage(NULL,
		m_strPath,
		IMAGE_BITMAP,
		0,
		0,
		LR_LOADFROMFILE);                                //载入位图文件
	CBitmap bmpPicture;
	CDC mdcPicture;
	CBitmap *bmpFromHandle = bmpPicture.FromHandle(bmpHandle);

	CRect rctPicture;
	GetDlgItem(IDC_STATIC)->GetWindowRect(&rctPicture);  //获取IDC_STATIC控件区域
	mdcPicture.CreateCompatibleDC(&dc);
	CBitmap * bmpPrevious = mdcPicture.SelectObject(bmpFromHandle);
	ScreenToClient(&rctPicture);                         //坐标转换
	dc.BitBlt(rctPicture.left,rctPicture.top,            //绘制图像
		rctPicture.Width(),rctPicture.Height(),
		&mdcPicture,0,0,SRCCOPY);

	dc.SelectObject(bmpPrevious);
	.
	.
	.
}
```

5.添加按钮处理函数:

```cpp
void CPictureBoxDlg::OnBnClickedButton1()
{
	//TODO: 在此添加控件通知处理程序代码
	CFileDialog dlg(TRUE,L"bmp",L".bmp",
		OFN_HIDEREADONLY | OFN_OVERWRITEPROMPT,
		L"位图文件(*.BMP)|*.BMP||");
	dlg.DoModal();
	m_strPath = dlg.GetPathName();  //获取用户选中的文件路径
	Invalidate();
}
```

6.运行结果：

![](/images/posts/MFC/32.png)