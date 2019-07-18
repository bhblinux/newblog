---
title: win32资源
author: Bhblinux
tags:
  - c
categories: []
date: 2018-04-22 11:55:00
---
# 资源文件

图标,光标,字符串,菜单,加速jm

## ICON

## 光标资源

```c
#define _WIN32_WINNT 0x0400  //¹öÂÖÏûÏ¢±ØÐë¼ÓÔÚwindows.hÖ®Ç°¼Ó
#include <windows.h>
#include <stdio.h>
#include <stdlib.h>

#include "resource.h"
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



BOOL OnSetCursor(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	int nHitText=LOWORD(lParam);
	if(HTCLIENT!=nHitText){
		return FALSE;
	}
	//获取窗口的客户区
	RECT rcClient={0};   
	GetClientRect(hWnd,&rcClient);
	//获得当前光标的位置
	POINT ptPos={0};
	GetCursorPos(&ptPos);
	ScreenToClient(hWnd,&ptPos); 
	//根据位置设置光标
	HCURSOR hCursor=NULL;
	if(ptPos.x<rcClient.right/2){
		if(ptPos.y<rcClient.bottom/2){
			//左上
			hCursor=LoadCursor(NULL,IDC_SIZEALL);
		}else{
			//左下
			hCursor=LoadCursor(NULL,IDC_HELP);

		}
	}else{
		if(ptPos.y<rcClient.bottom/2){
			//右上
			hCursor=LoadCursor(NULL,IDC_UPARROW);
		}else{
			//右下
			hCursor=LoadCursor(NULL,IDC_NO);
		}

	}

	//设置光标//返回原来就的cursor句柄
	SetCursor(hCursor);
	return TRUE;

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


	case WM_SETCURSOR:
		if(TRUE==OnSetCursor(hWnd,nMsg,wParam,lParam)){
			return 0;
		}
		break;
	case WM_DESTROY:

		PostQuitMessage(0); //wm_quit

		return 0;
	}
	//默认的消息处理函数会把cursor修改
	return DefWindowProc(hWnd,nMsg,wParam,lParam);

}

BOOL RegisterWnd(LPSTR pszClassName){
	WNDCLASSEX wce  ={0};
	wce.cbSize      =sizeof(wce);
	wce.style       =CS_HREDRAW|CS_VREDRAW|CS_DBLCLKS;
	wce.cbWndExtra  =0;
	wce.cbWndExtra  =0;
	wce.lpfnWndProc =  WndProc;
	wce.hIcon       = LoadIcon(g_hInst,MAKEINTRESOURCE(IDI_MAIN));
	wce.hCursor     = LoadCursor(NULL,MAKEINTRESOURCE(IDC_CURSOR1));
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


## 字符串资源

1. 使用 LoadString

```c

int LoadString(
  HINSTANCE hInstance,  // 程序句柄
  UINT uID,             // 字符串资源id
  LPTSTR lpBuffer,      // 存放字符串buffer
  int nBufferMax        // buff的大小
);
返回获取字符串的长度
CHAR szText[260]={0};
	LoadString(g_hInst,IDS_MAIN,szText,260);

```

## 菜单资源

1. 添加菜单资源
2. 加载菜单资源 

```c
HMENU LoadMenu(
  HINSTANCE hInstance,  //应用程序句柄
  LPCTSTR lpMenuName    // 菜单的id字符远程
);
返回加载成功的菜单句柄

```

3. 命令处理

使用添加的菜单id的宏,在wm_command消息中处理菜单命令

## 加速键资源

### 加速键的作用

可以使用加速键执行命令,例如CTRL+S存盘

### 使用(Accelerator)

1. 加载

LoadAccelerators

```c
HACCEL LoadAccelerators(
  HINSTANCE hInstance,  // 句柄
  LPCTSTR lpTableName   // 名称
  
);

```

2. 增加消息处理

TranslateAccelerator

```c
int TranslateAccelerator(  HWND hWnd,         // 处理加速键的窗口句柄
  HACCEL hAccTable,  // 加速键表的窗口句柄
  LPMSG lpMsg        // MSG结构
  
#define _WIN32_WINNT 0x0400  //¹öÂÖÏûÏ¢±ØÐë¼ÓÔÚwindows.hÖ®Ç°¼Ó
#include <windows.h>
#include <stdio.h>
#include <stdlib.h>

#include "resource.h"
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



BOOL OnSetCursor(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	int nHitText=LOWORD(lParam);
	if(HTCLIENT!=nHitText){
		return FALSE;
	}
	//获取窗口的客户区
	RECT rcClient={0};   
	GetClientRect(hWnd,&rcClient);
	//获得当前光标的位置
	POINT ptPos={0};
	GetCursorPos(&ptPos);
	ScreenToClient(hWnd,&ptPos); 
	//根据位置设置光标
	HCURSOR hCursor=NULL;
	if(ptPos.x<rcClient.right/2){
		if(ptPos.y<rcClient.bottom/2){
			//左上
			hCursor=LoadCursor(NULL,IDC_SIZEALL);
		}else{
			//左下
			hCursor=LoadCursor(NULL,IDC_HELP);

		}
	}else{
		if(ptPos.y<rcClient.bottom/2){
			//右上
			hCursor=LoadCursor(NULL,IDC_UPARROW);
		}else{
			//右下
			hCursor=LoadCursor(NULL,IDC_NO);
		}

	}

	//设置光标//返回原来就的cursor句柄
	SetCursor(hCursor);
	return TRUE;

}

void OnCommand(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	int nCmdID=LOWORD(wParam);
	switch(nCmdID){
	case ID_NEW:
		PrintLogln("ID_NEW");
		break;
	case ID_OPEN:
		PrintLogln("ID_OPEN");
		break;
	case ID_EXIT:
		PostQuitMessage(0);
		break;
	}
}

LRESULT CALLBACK WndProc(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	switch (nMsg)
	{
	case WM_COMMAND:
		OnCommand(hWnd,nMsg,wParam,lParam);
		break;

	case WM_SETCURSOR:
		if(TRUE==OnSetCursor(hWnd,nMsg,wParam,lParam)){
			return 0;
		}
		break;
	case WM_DESTROY:

		PostQuitMessage(0); //wm_quit

		return 0;
	}
	//默认的消息处理函数会把cursor修改
	return DefWindowProc(hWnd,nMsg,wParam,lParam);

}

BOOL RegisterWnd(LPSTR pszClassName){
	WNDCLASSEX wce  ={0};
	wce.cbSize      =sizeof(wce);
	wce.style       =CS_HREDRAW|CS_VREDRAW|CS_DBLCLKS;
	wce.cbWndExtra  =0;
	wce.cbWndExtra  =0;
	wce.lpfnWndProc =  WndProc;
	wce.hIcon       = LoadIcon(g_hInst,MAKEINTRESOURCE(IDI_MAIN));
	wce.hCursor     = LoadCursor(NULL,MAKEINTRESOURCE(IDC_CURSOR1));
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
	//加载字符串资源
	CHAR szText[260]={0};
	LoadString(g_hInst,IDS_MAIN,szText,260);
	//添加菜单资源
	//加载菜单资源
	HMENU hMenu=LoadMenu(g_hInst,MAKEINTRESOURCE(IDR_MAIN));
	HWND hWnd=CreateWindowEx(0,pszClassName,szText,
		WS_OVERLAPPEDWINDOW,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,
		NULL,hMenu,g_hInst,NULL);
	return hWnd;
}



void DisplayWnd(HWND hWnd){

	ShowWindow(hWnd,SW_SHOW);

	UpdateWindow(hWnd);
}
//ÏûÏ¢´¦Àí
void Message(HWND hWnd){
	//加载加速键
	HACCEL hAccel =LoadAccelerators(g_hInst,MAKEINTRESOURCE(IDR_ACCEL));
	MSG msg={0};
	while(GetMessage(&msg,NULL,0,0)){
		if(!TranslateAccelerator(hWnd,hAccel,&msg)){
			TranslateMessage(&msg);
			DispatchMessage(&msg);
		}
		

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
		Message(hWnd);

		return 0;

}

```

3. 关于加速键的消息

TranslateAccelerator 将WM_KEYDOWN或WM_SYSKEYDOWN消息翻译成WM_COMMAND或者WM_SYSCOMAND消息,当收到消息时,会根据加速键表中按键和命令id对应关系,找到相应的命令id,然后调用窗口处理函数,执行相应的消息,当找到对应命令id执行后,返回非零,就不再执行后续处理,否则,继续让消息循环中的TransnlateMessage和DISPATCHMESSAGE处理
