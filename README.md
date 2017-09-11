# -MFC
MFC相关内容

VC学习笔记1：按钮的使能与禁止

用ClassWizard的Member Variables为按钮定义变量，如：m_Button1；
则
m_Button1.EnableWindow(true); 使按钮处于允许状态
m_Button1.EnableWindow(false); 使按钮被禁止，并变灰显示


VC学习笔记2：控件的隐藏与显示

用CWnd类的函数BOOL ShowWindow(int nCmdShow)可以隐藏或显示一个控件。

例1：
CWnd *pWnd;
pWnd = GetDlgItem( IDC_EDIT1 );     //获取控件指针，IDC_EDIT为控件ID号
pWnd->ShowWindow( SW_HIDE );     //隐藏控件

例2：
CWnd *pWnd;
pWnd = GetDlgItem( IDC_EDIT1 );     //获取控件指针，IDC_EDIT为控件ID号
pWnd->ShowWindow( SW_SHOW );     //显示控件

以上方法常用于动态生成控件，虽说用控件的Create函数可以动态生成控件，但这种控件很不好控制，所以用隐藏、显示方法不失为一种替代手段。


VC学习笔记3：改变控件的大小和位置

用CWnd类的函数MoveWindow()或SetWindowPos()可以改变控件的大小和位置。

void MoveWindow(int x,int y,int nWidth,int nHeight);
void MoveWindow(LPCRECT lpRect);
第一种用法需给出控件新的坐标和宽度、高度；
第二种用法给出存放位置的CRect对象；
例：
CWnd *pWnd;
pWnd = GetDlgItem( IDC_EDIT1 );     //获取控件指针，IDC_EDIT1为控件ID号
pWnd->MoveWindow( CRect(0,0,100,100) );     //在窗口左上角显示一个宽100、高100的编辑控件

SetWindowPos()函数使用更灵活，多用于只修改控件位置而大小不变或只修改大小而位置不变的情况：
BOOL SetWindowPos(const CWnd* pWndInsertAfter,int x,int y,int cx,int cy,UINT nFlags);
第一个参数我不会用，一般设为NULL;
x、y控件位置；cx、cy控件宽度和高度；
nFlags常用取值：
SWP_NOZORDER：忽略第一个参数；
SWP_NOMOVE：忽略x、y，维持位置不变；
SWP_NOSIZE：忽略cx、cy，维持大小不变；
例：
CWnd *pWnd;
pWnd = GetDlgItem( IDC_BUTTON1 );     //获取控件指针，IDC_BUTTON1为控件ID号
pWnd->SetWindowPos( NULL,50,80,0,0,SWP_NOZORDER | SWP_NOSIZE );     //把按钮移到窗口的(50,80)处
pWnd = GetDlgItem( IDC_EDIT1 );
pWnd->SetWindowPos( NULL,0,0,100,80,SWP_NOZORDER | SWP_NOMOVE );     //把编辑控件的大小设为(100,80)，位置不变
pWnd = GetDlgItem( IDC_EDIT1 );
pWnd->SetWindowPos( NULL,0,0,100,80,SWP_NOZORDER );     //编辑控件的大小和位置都改变
以上方法也适用于各种窗口。


VC学习笔记4：什么时候设定视中控件的初始尺寸？

我在CFormView的视中加入了一个编辑控件，在运行时使它充满客户区，当窗口改变大小时它也跟着改变。
改变控件尺寸可以放在OnDraw()函数中，也可放在CalcWindowRect()函数中，当窗口尺寸发生变化时，它们都将被执行，且CalcWindowRect()函数先于OnDraw()函数，下例是在CalcWindowRect()函数中修改控件尺寸。
重载VIEW类的CalcWindowRect函数，把设定控件的尺寸的语句加入这个函数中。
例：
void CMyEditView::CalcWindowRect(LPRECT lpClientRect, UINT nAdjustType) 
{
     // TODO: Add your specialized code here and/or call the base class

     CFrameWnd *pFrameWnd=GetParentFrame(); //获取框架窗口指针

     CRect rect;
     pFrameWnd->GetClientRect(&rect); //获取客户区尺寸

     CWnd *pEditWnd=GetDlgItem(IDC_MYEDIT); //获取编辑控件指针，IDC_MYEDIT为控件ID号
     pEditWnd->SetWindowPos(NULL,0,0,rect.right,rect.bottom-50,SWP_NOMOVE | SWP_NOZORDER); //设定控件尺寸，bottom-50是为了让出状态条位置。

     CFormView::CalcWindowRect(lpClientRect, nAdjustType);
}


VC学习笔记5：单选按钮控件（Ridio Button）的使用

一、对单选按钮进行分组：
每组的第一个单选按钮设置属性：Group，Tabstop，Auto;其余按钮设置属性Tabstop，Auto。

如：
Ridio1、Ridio2、Ridio3为一组，Ridio4、Ridio5为一组

设定Ridio1属性：Group，Tabstop，Auto
设定Ridio2属性：Tabstop，Auto
设定Ridio3属性：Tabstop，Auto

设定Ridio4属性：Group，Tabstop，Auto
设定Ridio5属性：Tabstop，Auto

二、用ClassWizard为单选控件定义变量，每组只能定义一个。如：m_Ridio1、m_Ridio4。

三、用ClassWizard生成各单选按钮的单击消息函数，并加入内容：

void CWEditView::OnRadio1() 
{
     m_Ridio1 = 0;     //第一个单选按钮被选中
}

void CWEditView::OnRadio2() 
{
     m_Ridio1 = 1;     //第二个单选按钮被选中
}

void CWEditView::OnRadio3() 
{
     m_Ridio1 = 2;     //第三个单选按钮被选中
}

void CWEditView::OnRadio4() 
{
     m_Ridio4 = 0;     //第四个单选按钮被选中
}

void CWEditView::OnRadio5() 
{
     m_Ridio4 = 1;     //第五个单选按钮被选中
}

四、设置默认按钮：
在定义控件变量时，ClassWizard在构造函数中会把变量初值设为-1，只需把它改为其它值即可。
如：
//{{AFX_DATA_INIT(CWEditView)
m_Ridio1 = 0;     //初始时第一个单选按钮被选中
m_Ridio4 = 0;     //初始时第四个单选按钮被选中
//}}AFX_DATA_INIT


VC学习笔记6：旋转控件（Spin）的使用

当单击旋转控件上的按钮时，相应的编辑控件值会增大或减小。其设置的一般步骤为：
一、在对话框中放入一个Spin控件和一个编辑控件作为Spin控件的伙伴窗口，
设置Spin控件属性：Auto buddy、Set buddy integer、Arrow keys
设置文本控件属性：Number

二、用ClassWizard为Spin控件定义变量m_Spin，为编辑控件定义变量m_Edit，定义时注意要把m_Edit设置为int型。

三、在对话框的OnInitDialog()函数中加入语句：
BOOL CMyDlg::OnInitDialog() 
{
     CDialog::OnInitDialog();
    
     m_Spin.SetBuddy( GetDlgItem( IDC_EDIT1 ) );     //设置编辑控件为Spin控件的伙伴窗口
     m_Spin.SetRange( 0, 10 );     //设置数据范围为0-10
     return TRUE;
}

四、用ClassWizard为编辑控件添加EN_CHANGE消息处理函数，再加入语句：
void CMyDlg::OnChangeEdit1() 
{
     m_Edit = m_Spin.GetPos();     //获取Spin控件当前值
}

OK!


VC学习笔记7：程序结束时保存文件问题

在文档-视图结构中，用串行化自动保存文件在各种VC书上都有介绍。现在的问题是我不使用串行化，而是自己动手保存，当点击窗口的关闭按钮时，如何提示并保存文档。

用ClassWizard在文档类（CxxDoc）中添加函数CanCloseFrame()，再在其中加入保存文件的语句就可以了。
注：要保存的数据应放在文档类（CxxDoc）或应用程序类（CxxApp）中，不要放在视图类中。

例：
//退出程序
BOOL CEditDoc::CanCloseFrame(CFrameWnd* pFrame) 
{
     CFile file;
     if(b_Flag)     //b_Flag为文档修改标志，在修改文档时将其置为True
     {
         int t;
         t=::MessageBox(NULL,"文字已经改变，要存盘吗？","警告",
                 MB_YESNOCANCEL | MB_ICONWARNING);     //弹出提示对话框
         if(t==0 || t==IDCANCEL)
             return false;
         if(t==IDYES)
         {
             CString sFilter="Text File(*.txt)|*.txt||";
             CFileDialog m_Dlg(FALSE,"txt",NULL,OFN_HIDEREADONLY | OFN_OVERWRITEPROMPT,(LPCTSTR)sFilter,NULL);     //定制文件对话框

             int k=m_Dlg.DoModal();     //弹出文件对话框
             if(k==IDCANCEL || k==0)
                 return false;
             m_PathName=m_Dlg.GetPathName();     //获取选择的文件路径名
            
             file.Open(m_PathName,CFile::modeCreate | CFile::modeWrite);
             file.Write(m_Text,m_TextLen);     //数据写入文件
             file.Close();
         }
     }
     return CDocument::CanCloseFrame(pFrame);
}


VC学习笔记8：UpdateData()

对于可以接收数据的控件，如编辑控件来说，UpdateData()函数至关重要。当控件内容发生变化时，对应的控件变量的值并没有跟着变化，同样，当控件变量值变化时，控件内容也不会跟着变。
UpdateData()函数就是解决这个问题的。

UpdateData(true);把控件内容装入控件变量
UpdateData(false);用控件变量的值更新控件

如：有编辑控件IDC_EDIT1，对应的变量为字符串m_Edit1，
1、修改变量值并显示在控件中：
m_Edit1 = _T("结果为50");
UpdateData(false);
2、读取控件的值到变量中：
用ClassWizard为IDC_EDIT1添加EN_CHANGE消息处理函数，
void CEditView::OnChangeEdit1()
{
     UpdateData(true);
}
