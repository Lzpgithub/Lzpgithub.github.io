---
layout: post 
title: Tree Control控件应用
categories: MFC学习
description: Tree Control控件应用
keywords:  Tree Control, MFC
---

1.创建一个基于对话框的应用程序；

2.添加一个Tree Control和两个Static Text控件：

![](/images/posts/MFC/42.png)

设置Tree Control属性：

![](/images/posts/MFC/43.png)

![](/images/posts/MFC/44.png)

![](/images/posts/MFC/45.png)

![](/images/posts/MFC/46.png)

![](/images/posts/MFC/47.png)

![](/images/posts/MFC/48.png)

3.添加一个Tree Control变量：

![](/images/posts/MFC/49.png)

4.初始化Tree Control控件，在类CtreeDirDlg中OnInitDialog()函数添加初始化代码:

```cpp
BOOL CTreeDirDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();
	.
	.
	.
	// TODO: 在此添加额外的初始化代码
	//初始化树形控件节点
	TV_INSERTSTRUCT treeCtrlItem;
	HTREEITEM hTreeItem;
	treeCtrlItem.hInsertAfter = TVI_LAST;
	treeCtrlItem.item.mask = TVIF_TEXT|TVIF_PARAM;

	//设置第一级节点
	treeCtrlItem.hParent = TVI_ROOT;
	treeCtrlItem.item.pszText = L"C:";
	treeCtrlItem.item.lParam = 1;//根
	hTreeItem = m_tree.InsertItem(&treeCtrlItem);
	//设置第二级节点
	treeCtrlItem.hParent = hTreeItem ;
	treeCtrlItem.item.pszText = L"Program file";
	treeCtrlItem.item.lParam = 2;
	m_tree.InsertItem(&treeCtrlItem);
	treeCtrlItem.item.pszText = L"My Documents";
	treeCtrlItem.item.lParam = 2;
	m_tree.InsertItem(&treeCtrlItem);
	treeCtrlItem.item.pszText = L"Windows";
	treeCtrlItem.item.lParam = 2;
	m_tree.InsertItem(&treeCtrlItem);
	//设置第一节节点
	treeCtrlItem.hParent = TVI_ROOT;
	treeCtrlItem.item.pszText = L"D:";
	treeCtrlItem.item.lParam = 1;//根
	hTreeItem = m_tree.InsertItem(&treeCtrlItem);
	//设置第二级节点
	treeCtrlItem.hParent = hTreeItem ;
	treeCtrlItem.item.pszText = L"Tool";
	treeCtrlItem.item.lParam = 2;
	m_tree.InsertItem(&treeCtrlItem);
	treeCtrlItem.item.pszText = L"image";
	treeCtrlItem.item.lParam = 2;
	m_tree.InsertItem(&treeCtrlItem);
	treeCtrlItem.item.pszText = L"Sourse";
	treeCtrlItem.item.lParam = 2;
	m_tree.InsertItem(&treeCtrlItem);

	return TRUE;  // 除非将焦点设置到控件，否则返回 TRUE
}
```

5.给控件Tree Contrl添加消息处理：

![](/images/posts/MFC/50.png)

```cpp
void CTreeDirDlg::OnSelchangedTree1(NMHDR *pNMHDR, LRESULT *pResult)
{
	LPNMTREEVIEW pNMTreeView = reinterpret_cast<LPNMTREEVIEW>(pNMHDR);
	// TODO: 在此添加控件通知处理程序代码
	//NM_TREEVIEW* pNMTreeView = (NM_TREEVIEW*)pNMHDR;
	CString strPath;
	CString strInfo;
	//得到选择的项
	HTREEITEM currItem = m_tree.GetSelectedItem();
	TV_ITEM treeCtrlItem;
	treeCtrlItem.hItem = currItem;
	m_tree.GetItem(&treeCtrlItem);
	//获取得到项的文本
	strInfo = m_tree.GetItemText(m_tree.GetSelectedItem());
	long currlParam = treeCtrlItem.lParam;
	if(currlParam ==1)
	{
		//如果是根节点
		strPath = strInfo;
	}
	else
	{
		//如果不是根节点
		treeCtrlItem.hItem = m_tree.GetParentItem(m_tree.GetSelectedItem());
		m_tree.GetItem(&treeCtrlItem);
		currItem = treeCtrlItem.hItem;
		strPath = m_tree.GetItemText(currItem);
		strPath += L"\\";
		strPath += strInfo;

	}
	//设置显示被选择的项
	GetDlgItem(IDC_STATIC2)->SetWindowText(strPath);

	*pResult = 0;
}

```

程序运行结果：

![](/images/posts/MFC/51.png)