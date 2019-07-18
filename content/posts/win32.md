---
title: win32
author: Bhblinux
tags:
  - C
categories:
  - 编程
date: 2018-05-01 18:47:00
---
# 窗口注册和创建
1. WinMain入口函数的定义
2. windowProc函数的定义
3. 注册窗口类 RegisterClass|RegisterClassEx
4. 窗口创建 CreateWindow/CreateWindowEx
5. 窗口的显示和刷新ShowWindow|UpdateWindow
6. 消息处理 GetMessage|DispatchMessage
7. 窗口退出 WM_DESTROY|PostQuitMessage

## 窗口的注册

1. 窗口类的分类
	- 系统全局的窗口类,比如按钮,文本,编辑框,直接使用,不需要注册
    - 应用程序的全局窗口类,可以在exe和dll中使用的窗口类,
    - 局部窗口类,只能在本模块中使用的窗口类
   
2. 实现窗口类的注册
	- 系统全局的窗口类,直接使用
    - 应用程序的全局窗口,注册时需要增加CS_GLOBALCLASS定义方式WNDCLASS wc{0}; wc.style=CS_GLOBALCLASS; RegisterClass(&wc);
    - 局部窗口类,不能增加CS_GLOBALCLASS定义
    
     ```c
     typedef struct _WNDCLASSEX { 
    UINT    cbSize;  //结构体的大小
    UINT    style; 
    WNDPROC lpfnWndProc; 
    int     cbClsExtra; 
    int     cbWndExtra; 
    HANDLE  hInstance; 
    HICON   hIcon; 
    HCURSOR hCursor; 
    HBRUSH  hbrBackground; 
    LPCTSTR lpszMenuName; 
    LPCTSTR lpszClassName; 
    HICON   hIconSm; 
} WNDCLASSEX; 

     ```
     
 ### 窗口类的风格
 1. CS_HREDRAW 窗口水平变化,重新绘制窗口
 2. CS_VREDRAW 窗口垂直变化,重新绘制窗口
 3. CS_GLOBALCLASS 创建应用程序全局窗口类
 4. CS_DBCLICK 窗口可以接受鼠标双击消息
 5. CS_CLASSDC 所有这种类型的窗口使用同一个dc(输出设备)
 6. CS_OWNDC 每个窗口拥有自己的dc
 7. CS_PARENTDC 使用父窗口的dc
 8. CS_NOCLOSE 禁止关闭命令没有小红X
 
 ### 窗口类的附加数据 
 - 在窗口类的数据信息中添加自己信息,
 - cbClsExtra用于添加信息的内存大小
 - SetClassLong 将信息保存到内存中
 - GetClassLong 将信息从内存中取出
 
 ### 窗口的附加数据 
 - 在窗口的数据信息中添加自己信息,
 - cbWndExtra用于添加信息的内存大小
 - SetWindowLong 将信息保存到内存中
 - GetWindowLong 将信息从内存中取出

### 窗口类的相关函数
1. RegisterClass|RegisterClassEX 注册
-  UnregisterClass 注销
- GetClassInfo|GetClassInfoEx 获取信息
- GetClassName 获取窗口的窗口类名称
- GetClassLong|SetClasslONG 附加数据的设置和获取
- GetWindowLong|SetWinodwLong 窗口的附加数据设置和获取
 
### 创建窗口时如何定位窗口类
1. 先查找应用程序局部的窗口类
- 如果找到了名称一致的窗口类,会比较HINSTANCE句柄,如果相等,就使用找到的窗口类的信息创建窗口,如果不同,继续查找
- 如果为找到名称一致的窗口类,会继续在应用程序全局窗口类中查找执行4,否则执行5
- 全局窗口类找到一致的,使用找到的窗口类信息创建窗口
- 没找到,会去系统的全局窗口类中查找,找到创建窗口,找不到返回null

## 窗口创建
### 函数

- CreateWindowEx|CreateWindow

```c
HWND CreateWindowEx(
  DWORD dwExStyle,      // 扩展样式
  LPCTSTR lpClassName,  // pointer to registered class name
  LPCTSTR lpWindowName, // pointer to window name
  DWORD dwStyle,        // window style
  int x,                // horizontal position of window
  int y,                // vertical position of window
  int nWidth,           // window width
  int nHeight,          // window height
  HWND hWndParent,      // handle to parent or owner window
  HMENU hMenu,          // handle to menu, or child-window identifier
  HINSTANCE hInstance,  // handle to application instance
  LPVOID lpParam        // pointer to window-creation data
);

```
### 窗口风格和扩展风格
1. 窗口风格 ws_xxx
- 扩展风格 WS_EX_XXX
- WS_OVERLAPPED 窗口,层叠窗口
- WS_POPUP 弹出式窗口
- WS_CHILD 子窗口

### 父窗口和子窗口
1. WS_CHILD,将窗口的风格
2. CreateWindow时,可以指定父窗口指定父窗口
3. SetParent 设置父窗口



### MDI窗口的创建
1. 创建主窗口
- MDICLIENT窗口
- 创建一个MDI子窗口

```c
#include <stdio.h>
#include <Windows.h>
#include <stdlib.h>

HWND g_hMDIClient=NULL;
HINSTANCE g_hInstance;
//窗口处理函数
LRESULT CALLBACK MainProc(HWND hWnd,
							UINT nMsg,
							WPARAM wParam,
							LPARAM lParam
							)

{
	switch(nMsg)
	{
	case WM_DESTROY:
		PostQuitMessage(0);
		return 0;
	}
	return DefFrameProc(hWnd,g_hMDIClient,nMsg,wParam,lParam);
}
//子窗口处理函数
LRESULT CALLBACK ChildProc(HWND hWnd,
	UINT nMsg,
	WPARAM wParam,
	LPARAM lParam)
{
	return DefMDIChildProc(hWnd,nMsg,wParam,lParam);
}
//注册主窗口类
BOOL registerWnd(LPSTR pszClassName){
	WNDCLASSEX wce={0};
	wce.cbSize=sizeof(wce);
	wce.style=CS_HREDRAW|CS_VREDRAW;
	wce.cbClsExtra=0;
	wce.cbWndExtra=0;
	wce.hCursor=NULL;
	wce.hIcon=NULL;
	wce.lpfnWndProc=MainProc;
	wce.lpszClassName=pszClassName;
	wce.lpszMenuName=NULL;
	wce.hInstance=g_hInstance;
	wce.hbrBackground=(HBRUSH)(COLOR_BTNFACE+1);
	wce.hIconSm=NULL;
	ATOM nAtom= RegisterClassEx(&wce);
	if(0==nAtom){
		return FALSE;
	}
	return TRUE;
}

//

//注册子窗口类
BOOL registerChildWnd(LPSTR pszClassName){
	WNDCLASSEX wce={0};
	wce.cbSize=sizeof(wce);
	wce.style=CS_HREDRAW|CS_VREDRAW;
	wce.cbClsExtra=0;
	wce.cbWndExtra=0;
	wce.hCursor=NULL;
	wce.hIcon=NULL;
	wce.lpfnWndProc=ChildProc;
	wce.lpszClassName=pszClassName;
	wce.lpszMenuName=NULL;
	wce.hInstance=g_hInstance;
	wce.hbrBackground=(HBRUSH)(COLOR_WINDOW);
	wce.hIconSm=NULL;
	ATOM nAtom= RegisterClassEx(&wce);
	if(0==nAtom){
		return FALSE;
	}
	return TRUE;
}

//创建主窗口
HWND createWnd(LPSTR pszClassName)
{
	return CreateWindowEx(WS_EX_APPWINDOW,pszClassName,"MainWnd",WS_OVERLAPPEDWINDOW,CW_USEDEFAULT ,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,NULL,NULL,g_hInstance,NULL);

}
//创建mdi客户区
HWND CreateMDIClient(HWND hWndParent)
{
	CLIENTCREATESTRUCT cs={0};
	cs.idFirstChild=1000;
	HWND hWnd=CreateWindowEx(
		0,"MDICLIENT","MainWnd",
		WS_CHILD|WS_VISIBLE,CW_USEDEFAULT,
		CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,
		hWndParent,NULL,g_hInstance,&cs);
	return hWnd;
}


//创建子窗口
HWND createChildWnd(LPSTR pszClassName,HWND hWndParent)
{
	return CreateWindowEx(WS_EX_MDICHILD,pszClassName,"ChildMnd",WS_CHILD|WS_VISIBLE,CW_USEDEFAULT ,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,hWndParent,NULL,g_hInstance,NULL);

}

//显示窗口
void displayWnd(HWND hWnd){
	ShowWindow(hWnd,SW_SHOW);
	UpdateWindow(hWnd);
}
//消息处理
void message(){
	MSG msg={0};
	while(GetMessage(&msg,NULL,0,0)){
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}
}
int APIENTRY WinMain(
		HINSTANCE hInstance,
		HINSTANCE hPrevInstance,
		LPSTR lpCmdLine,
		INT nCmdShow
	)
{
	g_hInstance=hInstance;
	registerWnd("MainWnd");
	registerChildWnd("ChildWnd");
	//创建mdi主窗口
	HWND hMainWnd=createWnd("MainWnd");
	//创建MDICLIENT窗口
	g_hMDIClient=CreateMDIClient(hMainWnd);
	//创建MDI子窗口
	HWND hChild1=createChildWnd("ChildWnd",g_hMDIClient);
	HWND hChild2=createChildWnd("ChildWnd",g_hMDIClient);
	MoveWindow(g_hMDIClient,100,100,300,300,TRUE);
	MoveWindow(hChild1,100,100,200,100,TRUE);
	MoveWindow(hChild2,100,200,200,100,TRUE);
	displayWnd(hMainWnd);
	message();
	
	return 0;
}
```





# 其他函数
1. MoveWindow