---
title: Win32菜单
author: Bhblinux
tags:
  - c
categories:
  - 编程
date: 2018-04-21 11:37:00
---
# 菜单
## 菜单的使用
1. 菜单的创建

菜单创建后,会返回HMENU句柄
 - MENU 菜单 CreateMenu
 - POPUPMENU 弹出式菜单 CreatePopupMenu
 
 菜单项 每个菜单项会有一个id号,可以根据这个id执行不同的参数
2. 菜单的使用
	- CreateMenu
    
    - AppenedMenu 增加菜单项
    ```c
    BOOL WINAPI AppendMenu(
  _In_     HMENU    hMenu,
  _In_     UINT     uFlags,
  _In_     UINT_PTR uIDNewItem,
  _In_opt_ LPCTSTR  lpNewItem
);
    ```
    - CreatePopupMenu

```c
  
  #define _WIN32_WINNT 0x0400  //¹öÂÖÏûÏ¢±ØÐë¼ÓÔÚwindows.hÖ®Ç°¼Ó
#include <windows.h>
#include <stdio.h>
#include <stdlib.h>

#define WM_FIRSTMSG (WM_USER+1)//×Ô¶¨ÒåÏûÏ¢
HINSTANCE g_hInst=NULL;
HANDLE g_hStdOut=NULL;
int g_nXPos,g_nYPos;
int g_nX1Rect,g_nY1Rect;
int g_nX2Rect,g_nY2Rect;

BOOL OnSysCommand(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam){
	switch(wParam)
	{
	case SC_CLOSE:
		if(IDOK==MessageBox(NULL,"ÊÇ·ñ½«ÎÄ¼þ´æÅÌ","OnSysCommand",MB_OKCANCEL|MB_ICONWARNING)){
			return TRUE;
		}else{
			return FALSE;
		}
		break;
	}
}

void PrintLog(LPSTR pszLog){
	WriteConsole(g_hStdOut,pszLog,strlen(pszLog),NULL,NULL);

}
void PrintLogln(LPSTR pszLog){
	WriteConsole(g_hStdOut,pszLog,strlen(pszLog),NULL,NULL);
	WriteConsole(g_hStdOut,"\r\n",strlen("\r\n"),NULL,NULL);

}


void OnCreater(HWND hWnd,
	UINT nMsg,
	WPARAM wParam,
	LPARAM lParam)
{
	HMENU hMenu=CreateMenu();
	HMENU hFileMenu=CreatePopupMenu();
	AppendMenu(hFileMenu,MF_STRING|MF_CHECKED,1001,"新建(&N)");
	//分割线
	AppendMenu(hFileMenu,MF_SEPARATOR,0,NULL);
	AppendMenu(hFileMenu,MF_STRING,1002,"退出(&Q)");
	AppendMenu(hMenu,MF_STRING|MF_POPUP,(UINT)hFileMenu,"文件(&F)"); //快捷键alt+f
	
	HMENU hEditMenu=CreatePopupMenu();
	AppendMenu(hEditMenu,MF_STRING,1003,"剪切(&x)");
	AppendMenu(hEditMenu,MF_STRING,1004,"拷贝(&C)");
	AppendMenu(hEditMenu,MF_STRING,1005,"粘贴(&v)");
	AppendMenu(hMenu,MF_STRING|MF_POPUP,(UINT)hEditMenu,"编辑(&E)");
	HMENU hHelpMenu=CreatePopupMenu();
	AppendMenu(hHelpMenu,MF_STRING,1006,"关于(&A)");
	AppendMenu(hMenu,MF_STRING|MF_POPUP,(UINT)hHelpMenu,"帮助(&H)");
	SetMenu(hWnd,hMenu);
}


LRESULT CALLBACK WndProc(
	HWND hWnd,
	UINT nMsg,
	WPARAM wParam,
	LPARAM lParam
	)
{
	switch (nMsg)
	{
	case WM_CREATE:
		OnCreater(hWnd,nMsg,wParam,lParam);
		break;
	case WM_DESTROY:

		PostQuitMessage(0); //wm_quit

		return 0;
	}
	return DefWindowProc(hWnd,nMsg,wParam,lParam);

}

BOOL RegisterWnd(LPSTR pszClassName){
	WNDCLASSEX wce  ={0};
	wce.cbSize      =sizeof(wce);
	wce.style       =CS_HREDRAW|CS_VREDRAW|CS_DBLCLKS;
	wce.cbWndExtra  =0;
	wce.cbWndExtra  =0;
	wce.lpfnWndProc =  WndProc;
	wce.hIcon       = NULL;
	wce.hCursor     = NULL;
	wce.hbrBackground =(HBRUSH)(COLOR_BTNFACE+1);
	wce.hInstance     = g_hInst;
	wce.lpszClassName = pszClassName;
	wce.lpszMenuName=NULL;
	wce.hIconSm = NULL; 

	ATOM nAtom=RegisterClassEx(&wce);
	if( 0 == nAtom){
		return false;
	}

	return TRUE;
}

HWND CreateWnd(LPSTR pszClassName){
	HWND hWnd=CreateWindowEx(0,pszClassName,"MyWnd",
		WS_OVERLAPPEDWINDOW,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,
		NULL,NULL,g_hInst,NULL);
	return hWnd;
}



void DisplayWnd(HWND hWnd){

	ShowWindow(hWnd,SW_SHOW);

	UpdateWindow(hWnd);
}
//ÏûÏ¢´¦Àí
void Message(){
	MSG msg={0};
	while(GetMessage(&msg,NULL,0,0)){
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}
}



void NewConsole(){
	AllocConsole(); //²úÉúÒ»¸ö¿ØÖÆÌ¨µÄ´°¿Ú
	g_hStdOut=GetStdHandle(STD_OUTPUT_HANDLE);
	CHAR szText[]="Debug Message......:\n";
	WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
}

int APIENTRY WinMain(HINSTANCE hInstance,
	HINSTANCE hPrevInstance,
	LPTSTR    lpCmdLine,
	int       nCmdShow){

		g_hInst=hInstance;
		RegisterWnd("MyWnd");
		HWND hWnd=CreateWnd("MyWnd");
		DisplayWnd(hWnd);
		NewConsole();
		Message();

		return 0;

}
  
  ```
## 菜单的命令响应
 1. WM_COMMAND 消息
  
  当用户点击菜单,按钮等控件时,系统会想窗口发送WM_COMMAND消息
  
  - WPARAM: HIWORD - 通知消息的标识,LOWORD - id号
  - LPARAM: 控件的句柄,没有为空
 2. 命令处理
  
  根据菜单项的id号做相应处理
 3. 菜单项的状态
 当用户点击菜单,显示弹出菜单之前触发
  - WM_INITMENUPUPUP消息
   	- wparam 菜单句柄
    - lparam loword 菜单位置 hiword 是否是系统菜单
  - 修改菜单状态
   	- 根据wparam的菜单句柄,使用MENUAPi函数修改菜单状态
    
    ```C
    CheckMenuItem 选择
    EnableMenuItem 允许和禁止
    SetMenuItemInfo 可以设置更多信息
    ```
 ## 系统菜单
 
 执行系统提供的窗口命令
 
 ### 系统菜单的使用
 
 	1. 获取系统菜单 GetSystemMen
    
```c
    
    HMENU WINAPI GetSystemMenu(
  _In_ HWND hWnd, //获取的窗口句柄
  _In_ BOOL bRevert //获取时重置坐标
);

//获取系统菜单
	HMENU hSysMenu=GetSystemMenu(
		hWnd,    // handle to window to own window menu
		FALSE// reset flag
		);
        
    ```
   2. 修改系菜单,增加,删除
   	- AppendMenu
   	- InsertMenu
 
 ```c
 BOOL InsertMenu(
  HMENU hMenu,      // handle to menu
  UINT uPosition,   // menu item that new menu item precedes
  UINT uFlags,      // menu item flags
  UINT uIDNewItem,  // menu item identifier or handle to drop-down 
                    // menu or submenu
  LPCTSTR lpNewItem // menu item content
);
	//增加菜单项
	InsertMenu(hSysMenu,0,MF_BYPOSITION|MF_STRING,1001,"测试1");
	InsertMenu(hSysMenu,0,MF_BYPOSITION|MF_STRING,1002,"测试2");
 ```
   	- RemoveMen
    
    ```c
    BOOL RemoveMenu(
  HMENU hMenu,     // handle to menu
  UINT uPosition,  // menu item identifier or position
  UINT uFlags      // menu item flag
);

    ```
   3. 系统菜单的命令响应
   	- WM_SYSCOMMAND

```c
int nID=LOWORD(wParam);
	CHAR szText[10]={0};
	sprintf(szText,"%d",nID);
	/*switch(nID){
	case 1001:
	PrintLogln("haha");
	break;
	}*/
```

## 右键菜单

在窗口点击鼠标右键,弹出的菜单

###. 右键菜单的使用
1. 创建菜单

CreatePopupMenu

2. 菜单增加

AppendMenu

3. 菜单的显示

TrackPopupMenu

4. 右键消息

WM_CONTEXTMENU

```c
BOOL TrackPopupMenu(
  HMENU hMenu,         // 显示的菜单句柄
  UINT uFlags,         // 显示方式
  int x,               // 菜单在屏幕的x坐标位置
  int y,               // 菜单在屏幕的y坐标位置
  int nReserved,       // 必须为0
  HWND hWnd,           // 处理菜单命令的窗口句柄
  CONST RECT *prcRect  // 不需要NULL
);


```

4. 菜单的命令处理

WM_CMMAND

```c

hwnd = (HWND) wParam;   右键抬起时对应的窗口句柄
xPos = LOWORD(lParam);   x坐标
yPos = HIWORD(lParam);   y坐标

```

```c
#define _WIN32_WINNT 0x0400  //¹öÂÖÏûÏ¢±ØÐë¼ÓÔÚwindows.hÖ®Ç°¼Ó
#include <windows.h>
#include <stdio.h>
#include <stdlib.h>

#define WM_FIRSTMSG (WM_USER+1)//×Ô¶¨ÒåÏûÏ¢
HINSTANCE g_hInst=NULL;
HANDLE g_hStdOut=NULL;
int g_nXPos,g_nYPos;
int g_nX1Rect,g_nY1Rect;
int g_nX2Rect,g_nY2Rect;


void PrintLog(LPSTR pszLog){
	WriteConsole(g_hStdOut,pszLog,strlen(pszLog),NULL,NULL);

}
void PrintLogln(LPSTR pszLog){
	WriteConsole(g_hStdOut,pszLog,strlen(pszLog),NULL,NULL);
	WriteConsole(g_hStdOut,"\r\n",strlen("\r\n"),NULL,NULL);

}

//添加右键菜单
void OnRButtonUp(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	//创建弹出式菜单
	HMENU hMenu=CreatePopupMenu();
	//增加菜单项
	AppendMenu(hMenu,MF_STRING,1001,"测试1");
	AppendMenu(hMenu,MF_STRING,1002,"测试2");
	AppendMenu(hMenu,MF_SEPARATOR,0,NULL);
	AppendMenu(hMenu,MF_STRING,1003,"测试3");

	//显示菜单
	//ClientToScreen 把客户去的坐标转成屏幕坐标
	//鼠标的坐标
	int nX=LOWORD(lParam);
	int nY=HIWORD(lParam);
	POINT point={0};
	point.x=nX;
	point.y=nY;
	ClientToScreen(hWnd,&point);
	TrackPopupMenu(hMenu,TPM_LEFTALIGN,point.x,point.y,0,hWnd,NULL);
}
//添加右键菜单响应
void OnCommand(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	int nCmdID=LOWORD(wParam);
	switch(nCmdID)
	{
	case 1001:
		PrintLogln("测试1");
		break;
	case 1002:
		PrintLogln("测试2");
		break;
	}
	

}
void OnContextmenu(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	//创建弹出式菜单
	HMENU hMenu=CreatePopupMenu();
	//增加菜单项
	AppendMenu(hMenu,MF_STRING,1001,"测试1");
	AppendMenu(hMenu,MF_STRING,1002,"测试2");
	AppendMenu(hMenu,MF_SEPARATOR,0,NULL);
	AppendMenu(hMenu,MF_STRING,1003,"测试3");
	//显示菜单
	//ClientToScreen 把客户去的坐标转成屏幕坐标
	//鼠标的坐标
	int nX=LOWORD(lParam);
	int nY=HIWORD(lParam);
	//显示菜单
	TrackPopupMenu(hMenu,TPM_LEFTALIGN,nX,nY,0,hWnd,NULL);
	//删除菜单
	DestroyMenu(hMenu);
	

}


LRESULT CALLBACK WndProc(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	switch (nMsg)
	{
   case WM_CONTEXTMENU:
		OnContextmenu(hWnd,nMsg,wParam,lParam);
		break;
	case WM_RBUTTONUP:
		OnRButtonUp(hWnd,nMsg,wParam,lParam);
		break;
	case WM_COMMAND:
		OnCommand(hWnd,nMsg,wParam,lParam);
		break;
	case WM_DESTROY:

		PostQuitMessage(0); //wm_quit

		return 0;
	}
	return DefWindowProc(hWnd,nMsg,wParam,lParam);

}

BOOL RegisterWnd(LPSTR pszClassName){
	WNDCLASSEX wce  ={0};
	wce.cbSize      =sizeof(wce);
	wce.style       =CS_HREDRAW|CS_VREDRAW|CS_DBLCLKS;
	wce.cbWndExtra  =0;
	wce.cbWndExtra  =0;
	wce.lpfnWndProc =  WndProc;
	wce.hIcon       = NULL;
	wce.hCursor     = NULL;
	wce.hbrBackground =(HBRUSH)(COLOR_BTNFACE+1);
	wce.hInstance     = g_hInst;
	wce.lpszClassName = pszClassName;
	wce.lpszMenuName=NULL;
	wce.hIconSm = NULL; 

	ATOM nAtom=RegisterClassEx(&wce);
	if( 0 == nAtom){
		return false;
	}

	return TRUE;
}

HWND CreateWnd(LPSTR pszClassName){
	HWND hWnd=CreateWindowEx(0,pszClassName,"MyWnd",
		WS_OVERLAPPEDWINDOW,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,
		NULL,NULL,g_hInst,NULL);
	return hWnd;
}



void DisplayWnd(HWND hWnd){

	ShowWindow(hWnd,SW_SHOW);

	UpdateWindow(hWnd);
}
//ÏûÏ¢´¦Àí
void Message(){
	MSG msg={0};
	while(GetMessage(&msg,NULL,0,0)){
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}
}



void NewConsole(){
	AllocConsole(); //²úÉúÒ»¸ö¿ØÖÆÌ¨µÄ´°¿Ú
	g_hStdOut=GetStdHandle(STD_OUTPUT_HANDLE);
	CHAR szText[]="Debug Message......:\n";
	WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
}

int APIENTRY WinMain(HINSTANCE hInstance,
	HINSTANCE hPrevInstance,
	LPTSTR    lpCmdLine,
	int       nCmdShow){

		g_hInst=hInstance;
		RegisterWnd("MyWnd");
		HWND hWnd=CreateWnd("MyWnd");
		DisplayWnd(hWnd);
		NewConsole();
		Message();

		return 0;

}

```
