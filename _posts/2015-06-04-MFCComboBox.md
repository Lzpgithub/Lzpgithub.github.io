---
layout: post 
title: Combo Box控件应用
categories: MFC学习
description: 对话框菜单栏的创建MFC
keywords:  ComboBox, MFC
---

1.创建一个基于对话框的应用程序；

2.添加一个Static text控件和一个Combo Box控件：

设置Combo Box属性：

![](/images/posts/MFC/21.png)

3.初始化Combo Box控件，在类CFontSizeDlg中OnInitDialog()函数：

```cpp
BOOL CFontSizeDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();
	.
	.
	.
	// TODO: 在此添加额外的初始化代码
	//初始化Combo Box控件
	CString strTemp;
	((CComboBox*)GetDlgItem(IDC_COMBO1))->ResetContent();
	//添加项
	((CComboBox*)GetDlgItem(IDC_COMBO1))->AddString(_T("选择字体大小..."));
	//添加1~20的字号选择项
	for(int i = 1;i <= 40;i++)
	{
		strTemp.Format(L"%d",i);
        ((CComboBox*)GetDlgItem(IDC_COMBO1))->AddString(strTemp);
	}
	return TRUE;     // 除非将焦点设置到控件，否则返回 TRUE
}
```

4.给控件Combo Box添加消息处理函数:

![](/images/posts/MFC/22.png)

```cpp
void CFontSizeDlg::OnSelchangeCombo1()
{
	// TODO: 在此添加控件通知处理程序代码
	//获得选择的字号
	int index = ((CComboBox*)GetDlgItem(IDC_COMBO1))->GetCurSel();
	//创建新的字体
	CFont* pFont = new CFont;
	if(pFont != NULL)
		pFont->CreateFontW(
		index,0,0,0,FW_NORMAL,FALSE,
		FALSE,0,DEFAULT_CHARSET,OUT_DEFAULT_PRECIS,
		CLIP_DEFAULT_PRECIS,DEFAULT_QUALITY,
		DEFAULT_PITCH | FF_SWISS,L"仿宋");
	//设置控件字体
	GetDlgItem(IDC_STATIC)->SetFont(pFont);
}
```

5.程序运行效果：

![](/images/posts/MFC/23.png)

