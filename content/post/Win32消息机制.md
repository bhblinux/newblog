---
title: Win32消息机制
author: Bhblinux
tags:
  - C
categories:
  - 编程
date: 2018-04-16 22:26:00
---
# 消息机制
## 过程驱动
- 程序是安装我们预定好的顺序执行
## 事件驱动
- 程序的执行顺序是无序的,某个时间点所执行的代码,是由外界通知
- win32的消息机制,就是事件驱动
# win32消息程序
## win32窗口注册
## win32窗口创建
## win32消息循环
### GetMessage

```c
BOOL GetMessage（LPMSG lpMsg，//存放获取到的消息数据
			HWND hWnd， //获取消息的窗口句柄
        UINT wMsgFilterMin，//消息过滤的起始消息
        UINT wMsgFilterMax); //消息过滤的终止消息
返回值BOOL:成功获取消息,返回TRUE, 当获取WM_QUIT消息时,返回fase
switch (nMsg)
	{
	case:WM_DESTROY://窗口销毁时的消息
		PostQuitMessage(0); //wm_quit
		return 0;
	}
MSG - 由系统填写
hWnd - 接收由hWnd指定的窗口的消息 

```
### TranslateMessage
- 将键盘消息转换成字符消息
1. 首先检查是否为键盘按键消息
2. 如果发现是键盘按键消息,将会根据按键产生一个字符消息,在下一个GetMessage执行之前,会收到这个消息
3. 如果未发现,不做处理

### DispatchMessage
- 根据消息数据内窗口句柄,找到这个窗口的处理函数,并调用,进行消息处理
- 如果msg中,hwnd窗口句柄为空,则不做处理
```c
//消息处理函数
LRESULT CALLBACK WndProc(
	HWND hWnd,
	UINT nMsg,
	WPARAM wParam,
	LPARAM lParam
	)
{
	switch (nMsg)
	{
	case:WM_DESTROY://窗口销毁时的消息
		PostQuitMessage(0); //wm_quit
		return 0;
	}

	//使用系统函数处理我们未处理的消息类型
	return DefWindowProc(hWnd,nMsg,wParam,lParam);

}
```

## win32基本消息
1. WM_DESTROY 
- 窗口销毁时的消息,退出,释放内存
2. WM_CREATE 
- 窗口创建后窗口处理函数收到的第一条消息,做数据初始化/创建子窗口
- WPARAM wParam 不使用
- LPARAM lParam -LPCREATESTRUCT指针


3. WM_SIZE
- 当窗口大小发生变化是,会收到该消息
- 可以在该消息中调整窗口布局
4. WM_SYSCOMMAND
- 系统命令消息,当点击系统菜单和按钮时,会收到该消息
5. WM_PAINT 绘图消息
6. 键盘消息
7. 鼠标消息
8. WM_TIMER定时器消息
## 消息结构
- MSG
```c
typedef struct tagMSG { 
  HWND   hwnd; //消息的窗口句柄
  UINT   message; //消息标识
  WPARAM wParam; //消息的参数
  LPARAM lParam; //消息的参数
  DWORD  time; //消息产生的时间
  POINT  pt; //消息产生是,鼠标的位置
} MSG, *PMSG, *LPMSG;

```
## 消息的获取和发送
1. 获取GetMessage/PeekMessage
* GetMessage 阻塞函数
* PeekMessage 非阻塞函数
2. 发送SendMessage/PostMessage
* SendMessage 发送消息并等消息处理完成后返回 
* PostMessage 发送消息后立即返回

## 消息组成和分类
### 组成
- 窗口句柄/消息id/消息参数
### 分类
1. 系统消息:由系统定义和使用的消息
2. 用户自定义的消息,WM_USER(0X0400),从WM_USER的ID开始,到OX7FFF是可以使用的消息

```java
#define WM_FIRSTMSG (WM_USER+1)//自定义消息
case WM_FIRSTMSG: //接受消息
		MessageBox(NULL,"firstmsg","firstmsg",MB_OK); 
		break;
SendMessage(hWnd,WM_FIRSTMSG,0,0); //发送消息
```

## 消息队列
- 消息队列 先入先出
### 系统消息队列
1. 系统消息队列
2. 应用程序消息队列(线程)
### 消息和消息队列
1. 队列消息
 -  首先存放到消息队列当中,然后由GetMessage/PeekMessage取出,然后进行处理
 
 - 例如:鼠标消息/键盘消息 
 
2. 非队列消息
	- 直接发送消息处理窗口
    
## 消息获取
### 消息循环
1. GetMessage 从队列中获取消息,判断是否是WM_QUIT消息,如佛发现WM_QUIT消息,消息循环结束,否则继续下一步,
2. TranslageMessage 翻译按键消息,如果发现有按键消息,产生字符消息放入消息队列,继续下一步
3. DispatchMessage 找到消息所发窗口的处理函数,处理消息,处理完成后返回1

### GetMessage和PeekMessage
1. 从线程消息队列是获取消息,如果找到消息,就返回消息,进行消息处理.
2. 查找消息对流,通过向系统消息队列查询,如果找到消息,获取消息,返回并执行
3. 如果还未找到,检查窗口需要重新绘制的范围,如果发现存在重新绘制的范围,会产生WM_PAINT消息
4. 检查TM_TIMER定时器消息
5. 执行内存管理工作

### WM_PAINT消息
- 由于窗口的互相覆盖等,产生需要绘制的区域,那么会产生WM_PAIN刁曦,不直接发送WM_PAINT消息,通过api(InvalidateRect)声明需要绘制区域,来产生WM_PAINT消息
- 如果一个消息队列中有多个WM_PAINT消息,只有最后一个消息起作用
- WM_PAINT消息处理中,要清空需要被绘制的区域,BeginPaint

```java
void OnPaint(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	PAINTSTRUCT ps={0};
    //必须调用
	HDC hDc=BeginPaint(hWnd,&ps);
	CHAR szText[]="hello wm_paint";
	TextOut(hDc,100,100,szText,strlen(szText));
	//画一个矩形
	Rectangle(hDc,200,200,300,300);
    //必须调用
	EndPaint(hWnd,&ps);
}

```

### 键盘消息
1. 键盘消息

 1. WM_KEYDOWN
 2. WM_KEYUP
 3. WM_SYSKEYDOWN ALT/F10
 4. WM_SYSKEYUP
 5. WM_CHAR
2. 参数:WMAPAM - 虚拟键码  LPARAM - 相关的按键信息
3. 使用
 1. 当有按键消息时,先进入系统消息队列,然后被程序的消息循环获取
 2. 消息处理
 
 
 ```c
void NewConsole(){
	AllocConsole(); //产生一个控制台的窗口
	g_hStdOut=GetStdHandle(STD_OUTPUT_HANDLE);
	CHAR szText[]="Debug Message......:\n";
	WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
}
 case WM_KEYDOWN:
		{
        //获取虚拟按键
			switch(wParam){
			case VK_LEFT:
				{
					CHAR szText[]="vk_left\n";
					WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
				}
				break;
			case VK_RIGHT:
				{
					CHAR szText[]="VK_RIGHT\n";
					WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
				}
			}
			CHAR szText[]="WM_KEYDOWN\n";
			WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
		}
		
		break;
	case WM_KEYUP:
		{
			CHAR szText[]="WM_KEYUP\n";
			WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
		}
		break;
	case WM_SYSKEYDOWN:
		{
			CHAR szText[]="WM_SYSKEYDOWN\n";
			WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
		}
		
		break;
	case WM_SYSKEYUP:
		{
			CHAR szText[]="WM_SYSKEYUP\n";
			WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
		}
		
		break;
	case WM_CHAR:
		{
			CHAR szText[256]={0};
			sprintf(szText,"wm_char:%c\n",wParam); //获取按下键的字符
			WriteConsole(g_hStdOut,szText,strlen(szText),NULL,NULL);
		}
		
		break;
 
 ```
 
 ### 鼠标消息
 1. 鼠标消息
  1. WM_LBUTTONDOWN  左键按下
  2. WM_LBUTTONUP   左键抬起
  3. WM_RBUTTONDOWN  右键按下
  4. WM_RBUTTONUP   右键抬起
  5. WM_MOUSEMOVE   鼠标移动
  6. WM_MOUSEWHEEL   滚轮消息
  7. WM_LBUTTONDBLCLK 鼠标左键双击
  8. WM_RBUTTONDBLCLK 鼠标右键双击
 2.消息的参数
 
 ```c
 	WPARAM wParam; 当前键盘和鼠标按键状态例如MK_CONTRL..
	LPARAM lParam; 当前鼠标的坐标
    x坐标 低16位
    y坐标 高16位
   ClientToScreen 可以将坐标转换成显示器的坐标点
   

 ```
 
 3. 滚轮消息
 	1. 要在windwos.h 之前添加宏定义 #define _WIN32_WINNT 0x0400  
    
   2. 参数 WPARAM - LOWORD(wparam) 表示按键状态HIWORD(WPARAM)滚轮滚动幅度,120的倍数,可以为正负值,正,屏幕下移,负,相反 
   
```c
#define _WIN32_WINNT 0x0400  //滚轮消息必须加在windows.h之前加
#include <windows.h>
#include <stdio.h>
#include <stdlib.h>

#define WM_FIRSTMSG (WM_USER+1)//自定义消息
HINSTANCE g_hInst=NULL;
HANDLE g_hStdOut=NULL;
int g_nXPos,g_nYPos;
int g_nX1Rect,g_nY1Rect;
int g_nX2Rect,g_nY2Rect;

BOOL OnSysCommand(HWND hWnd,UINT nMsg,WPARAM wParam,LPARAM lParam){
	switch(wParam)
	{
	case SC_CLOSE:
		if(IDOK==MessageBox(NULL,"是否将文件存盘","OnSysCommand",MB_OKCANCEL|MB_ICONWARNING)){
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
void OnPaint(HWND hWnd,	UINT nMsg,WPARAM wParam,LPARAM lParam)
{
	PAINTSTRUCT ps={0};
	HDC hDC=BeginPaint(hWnd,&ps);
	CHAR szText[]="hello mouse";
	TextOut(hDC,g_nXPos,g_nYPos,szText,strlen(szText));


	Rectangle(hDC,g_nX1Rect,g_nY1Rect,g_nX2Rect,g_nY2Rect);
	EndPaint(hWnd,&ps);

}


//消息处理函数
LRESULT CALLBACK WndProc(
	HWND hWnd,
	UINT nMsg,
	WPARAM wParam,
	LPARAM lParam
	)
{
	switch (nMsg)
	{
	case WM_PAINT:
		OnPaint(hWnd,nMsg,wParam,lParam);
		break;
	case WM_LBUTTONDOWN:
		{
			g_nX1Rect=LOWORD(lParam);
			g_nY1Rect=HIWORD(lParam);
			PrintLogln("WM_LBUTTONDOWN");
		}
		break;
	case WM_LBUTTONUP:
		{
			g_nX2Rect=LOWORD(lParam);
			g_nY2Rect=HIWORD(lParam);
			InvalidateRect(hWnd,NULL,TRUE);
			PrintLogln("WM_LBUTTONUP");
		}
		break;
	case WM_RBUTTONDOWN:
		{
			PrintLogln("WM_RBUTTONDOWN");
		}
		break;
	case WM_RBUTTONUP:
		{
			PrintLogln("WM_RBUTTONUP");
		}
		break;
	case WM_LBUTTONDBLCLK://双击要加上该风格wce.style       CS_DBLCLKS
		{
			PrintLogln("WM_LBUTTONDBLCLK");
		}
		break;
	case WM_MOUSEWHEEL://滚轮消息
		{
			// 
			short zDelta=HIWORD(wParam);
			int nX= LOWORD(lParam);
			int nY=HIWORD(lParam);
			CHAR szText[260]={0};
			sprintf(szText,"WM_MOUSEWHEEL zDelta %x,x:%d, y:%d",zDelta,nX,nY);
			PrintLogln(szText);
		}
		break;
	case WM_MOUSEMOVE:
		{
			int nX=LOWORD(lParam);
			int nY=HIWORD(lParam);
			
			POINT ptScreen={0};
			ptScreen.x=nX;
			ptScreen.y=nY;
			ClientToScreen(hWnd,&ptScreen);

			CHAR szText[260]={0};
			sprintf(szText,"nx:%d,ny:%d",ptScreen.x,ptScreen.y);
			PrintLogln(szText);

			if(wParam & MK_CONTROL){ //按住ctrl键+鼠标移动
				PrintLogln("WM_MOUSEMOVE:MK_CONTROL");
			}
			if(wParam & MK_LBUTTON){  //按住鼠标左键并移动
				PrintLogln("WM_MOUSEMOVE:MK_LBUTTON");
			}

			g_nXPos=LOWORD(lParam);
			g_nYPos=HIWORD(lParam);
			InvalidateRect(hWnd,NULL,TRUE);

		}
		break;
	case WM_DESTROY://窗口销毁时的消息
		//PostMessage();
		PostQuitMessage(0); //wm_quit
		return 0;
	}
	return DefWindowProc(hWnd,nMsg,wParam,lParam);

}
//注册窗口类
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

//创建窗口
HWND CreateWnd(LPSTR pszClassName){
	HWND hWnd=CreateWindowEx(0,pszClassName,"MyWnd",
		WS_OVERLAPPEDWINDOW,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,
		NULL,NULL,g_hInst,NULL);
	return hWnd;
}


//显示窗口
void DisplayWnd(HWND hWnd){

	ShowWindow(hWnd,SW_SHOW);

	UpdateWindow(hWnd);
}
//消息处理
void Message(){
	MSG msg={0};
	while(GetMessage(&msg,NULL,0,0)){
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}
}



void NewConsole(){
	AllocConsole(); //产生一个控制台的窗口
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

### 定时器消息
1. 定时器消息 WM_TIMER

- 按照定时器设置时间段,自动想窗口发送一个定时器消息WM_TIMMER,优先级比较低

2. 消息和函数
	1. WM_TIMER 消息id
    
    ```c
    wParam:定时器id
    lParam:定时器的处理函数
    ```
    
    
   2. SetTimer 设置一个定时器
   
   ```c
   UINT_PTR WINAPI SetTimer(
  _In_opt_ HWND      hWnd, 窗口的句柄,可以为null
  _In_     UINT_PTR  nIDEvent, 定时器id 0位不预设id
  _In_     UINT      uElapse, 定时器时间间隔,毫秒级别
  _In_opt_ TIMERPROC lpTimerFunc 定时器处理函数,返回一个创建好的定时器
);
   ```
	3. KillTimer 结束一个定时器
    
    ```c
    BOOL WINAPI KillTimer(
  _In_opt_ HWND     hWnd, 窗口句柄
  _In_     UINT_PTR uIDEvent  定时器的id
);
    ```
   4. TimerProc 定时器处理函数
    
    ```c
    VOID CALLBACK TimerProc(
  __in  HWND hwnd, //窗口句柄
  __in  UINT uMsg, //wm_timer消息id
  __in  UINT_PTR idEvent, //定时器id
  __in  DWORD dwTime //当前系统时间
);
	//使用TimerProc处理函数创建定时器
	SetTimer(hWnd,1002,7000,TimerProcl);  //如果参数1为空,则会继续工作,但是没有timerProc处理函数的化则该定时器不工作
//定时器处理函数
void CALLBACK TimerProcl(HWND hWnd,UINT uMsg,UINT idEvent,DWORD dwTime ){
	PrintLogln("TimerProcl");
}


    ```
3. 使用方式
	1. 创建定时器
    2. 处理消息
    3. 结束定时器