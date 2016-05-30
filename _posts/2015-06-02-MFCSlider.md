---
layout: post
title: Slider控件应用
categories: MFC学习
description: 对话框菜单栏的创建MFC
keywords:  slider, MFC
---

1.创建一个基于对话框的应用程序；

2.给对话框添加一个Slider控件和3个Static控件：

![](/images/posts/MFC/10.png)

同时修改Slider控件下面static的属性，并修改其ID：

![](/images/posts/MFC/11.png)

3.给Slider控件添加变量：

![](/images/posts/MFC/12.png)

![](/images/posts/MFC/13.png)


4.在CSliderDlg类的OnInitDialog()函数中设置Slider的范围及刻度单位：

```cpp
BOOL CSliderDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();
	.
	.
	.
	.
	// TODO: 在此添加额外的初始化代码
	m_Slider.SetRange(0,100);//设置Slider的范围（0~100）
	m_Slider.SetTicFreq(10); //设置Slider刻度单位1

	return TRUE;  // 除非将焦点设置到控件，否则返回 TRUE
}
```

5.为Slider处理函数添加代码，获取Slider当前值，并将其显示到Static控件上:

![](/images/posts/MFC/14.png)

![](/images/posts/MFC/15.png)

![](/images/posts/MFC/16.png)

并在处理函数中添加相关代码：

```cpp
void CSliderDlg::OnCustomdrawSlider1(NMHDR *pNMHDR, LRESULT *pResult)
{
	LPNMCUSTOMDRAW pNMCD = reinterpret_cast<LPNMCUSTOMDRAW>(pNMHDR);
	// TODO: 在此添加控件通知处理程序代码
	CString str;
	str.Format(L"%d",m_Slider.GetPos());//读取滚动条值
	GetDlgItem(IDC_STATIC_VALUE)->SetWindowTextW(str);//显示滚动条值

	*pResult = 0;
}
```


6.程序运行效果：

![](/images/posts/MFC/17.png)