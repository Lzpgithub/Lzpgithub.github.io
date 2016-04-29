---
layout: post
title: 基于MFC的串口程序开发
categories: 个人项目
description: 基于MFC的串口程序开发
keywords:  串口, 软件, MFC
---

在嵌入式开发过程中，常常需要对单片机采集的数据进行数据分析，要分析数据只能在PC上分析，因此我们必须把单片机采集到的数据上传的PC上，可以有两种方式，一种是通过串口线连接单片机和PC，另一种是通过蓝牙串口进行连接。这样所需的硬件就完备了，但是还不够，因为我们把数据上传以后PC如何接收数据，如何分析数据，如何保存数据，这都需要PC软件来完成。本项目就是通过MFC编写串口程序来进行接收数据并实现画图功能。


##### 创建基于对话框程序

创建一个基于对话框程序，然后添加一个编辑框控件IDC_EDIT1，主要是显示接收到的数据值，然后是添加一个图片控件IDC_IMG，把接收到的数据进行画图。添加按钮控件，包括“打开串口”，“开始转换”和“绘图”按钮。最为重要的一个控件就是串口控件，必须给对话框添加串口控件：

![](/images/posts/Projection/9.png)

![](/images/posts/Projection/10.png)

![](/images/posts/Projection/11.png)

#### 初始化串口控件

在对话框初始化函数中OnInitDialog()添加对窗口控件进行初始化代码，包括设置PC的串口号，波特率、校验位等等。

```cpp
if(! m_comm1.get_PortOpen())//判断串口是否已经打开
    {
    m_comm1.put_CommPort(14);   //选择串口号14
    m_comm1.put_PortOpen(TRUE); //打开串口
    m_comm1.put_RThreshold(1);  //收到两个字节引发OnComm事件，执行OnComm处理程序
    m_comm1.put_InputMode(1);   //输入模式选为二进制
    m_comm1.put_Settings(L"115200,n,8,1"); //设置串口参数，波特率115200，无奇偶校验，1位停止位，8位数据位
	m_comm1.put_InputLen(0);    //设置当前接收区数据长度为0
    m_comm1.get_Input();        //先预读缓冲区以清除残留数据
    MessageBox(L"串口初始化完毕",L"提示"); //提示串口成功初始化
    }
    else MessageBox(L"串口被占用",L"提示"); //如果已经打开串口，消息框提醒

    pbar = (CProgressCtrl*)GetDlgItem(IDC_PROGRESS1);  //获得指向进度条IDC_PROGRESS1的指针
    pbar -> SetRange(0,4095);   //设置进度条的范围0~4095
    pbar -> SetPos(0);          //当前位置为0
    m_serial.SetWindowText(L"关闭串口");//按钮显示状态改变，因为此时串口已经打开
```

#### 给串口控件添加OnComm消息

在OnCommMscomm1()处理函数中添加接收数据代码，一边接收数据，一边进行进行数据显示，把数据送到画图函数进行画图，代码如下：
```cpp
void CMyCommDlg::OnCommMscomm1()
{
	// TODO: 在此处添加消息处理程序代码
	VARIANT variant1;             //定义VARIANT型变量，用于存放接收到的数据
    COleSafeArray safearray;      //定义safearray型变量

    LONG len,k;                   //定义长整型变量len,k
    BYTE rxdata[2048];            //定义BYTE型数组
    CString stremp1,stremp2,stremp3,stremp4;//定义两个字符串
	char ok_char[14]="";

    if(m_comm1.get_CommEvent()==2) //判断引起OnComm时间的原因
    {                              //如果是接收到特定2个字节数，则读取接收到的数据
    	variant1 = m_comm1.get_Input();//把接收到的数据存放到VARIANT型变量里
    	safearray = variant1;          //VARIANT型变量转换为ColeSafeArray型变量
    	len = safearray.GetOneDimSize();//得到数据的长度

    	for(k=0;k<len;k++)
    	{
    		safearray.GetElement(&k,rxdata+k); //得到接收到的数据放到BYTE型数组rxdata里
    	}

		gllen++;//全局的变量，对接收到的转换结果的个数进行计算
    	stremp2.Format(L"第%d次转换结果：",gllen);//显示第几次转换
		strRXDdata += stremp2;//把新的数据放到全局的字符串里  

		for(k=0;k<len;k++) //将数组转换为Cstring型变量
		{
			BYTE bt=*(char*)(rxdata+k); //字符型
			stremp3.Format(L"%c",bt); //将字符送入临时变量strtemp存放
			if(k<=12)
			ok_char[k]=bt;
        	stremp1+=stremp3; //加入接收编辑框对应字符串
		}

		int intnum;	
		sscanf(ok_char+3,"0x%04x",&intnum);
		stremp4.Format(_T("%2.2f"), (3.3*intnum/4096.0));
		strRXDdata+=stremp4;                     //加入接收编辑框对应字符串
		SetDlgItemText(IDC_STATIC,(L"当前电压值为： "+stremp4+" V")); //更新静态文本控件
		pbar -> SetPos(intnum);                  //更新进度条的当前位置
///////////////////////////////////////////////////////////////////////////////
		if(boolzhi)
		{

			//如果counter=106
			if(counter>105)//一共采集106个点做一次循环
			{
				counter=0;
				int_number[counter] = 100*(3.3*intnum/4096.0);
				counter++;//counter=1,m_counter=107
			}
			else
			{//counter=105
				int_number[counter] = 100*(3.3*intnum/4096.0);
	   			counter++;//counter=106
			}

			m_counter++;
			// 重绘	
			OnPaint();
			InvalidateRect(m_MouseRect, TRUE);
	
    	}
	}
///////////////////////////////////////////////////////////////////////////////	
    strRXDdata += " V\r\n";//字符串加单位V后换行
    SetDlgItemText(IDC_EDIT1,strRXDdata);//更新文本控件的显示
	UpdateData(FALSE); //更新编辑框内容
}
```

打开串口按钮的代码：

```cpp
void CMyCommDlg::OnBnClickedButton2()
{
	// TODO: 在此添加控件通知处理程序代码
	if(! m_comm1.get_PortOpen())           //判断串口是否已经打开
    {  
       m_comm1.put_PortOpen(TRUE);         //如果串口是关闭的，则打开串口
       m_serial.SetWindowText(L"关闭串口"); //按钮显示状态改变
    }
    else
    {
       m_comm1.put_PortOpen(FALSE);        //如果已经打开串口，则关闭串口
       m_serial.SetWindowText(L"打开串口"); //按钮显示状态改变
    }

}
```

最后是程序运行的效果：

![](/images/posts/Projection/12.png)