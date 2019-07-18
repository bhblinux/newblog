---
title: QT5
author: Bhblinux
tags:
  - c
categories:
  - 编程
date: 2018-04-27 22:09:00
---
## hello

```qt
hello.pro
SOURCES += \
    hello.cpp
QT+=widgets gui


main.cpp
/*应用程序抽象类*/
#include<QApplication>
/*窗口类*/
#include<QWidget>

int main(int argc,char *argv[]){
    QApplication app(argc,argv);
    QWidget w;
    w.setWindowTitle("hello world");
    w.show();
    /*exec 中有一个消息循环*/
    return app.exec();

}


```


## 手动编译qt文件

```qt
main.cpp

#include<QApplication>
#include<QLabel>

int main(int argc,char **argv){
    QApplication app(argc,argv);
    QLabel *label=new QLabel;
    label->setText("hello qt");
    label->show();
    return app.exec();
}

找到qt里面的 MinGW 4.8 (32-bit) Qt 5.2.0 for Desktop (MinGW 4.8 32 bit) 控制台
1. D:\Workspaces\QT\day0429\demo01>qmake -project
生成  demo01.pro 包
2 D:\Workspaces\QT\day0429\demo01>qmake  生成makefile
3. 编译 D:\Workspaces\QT\day0429\demo01>mingw32-make



```

## QT center 快捷键

```c

ctrl+h 横向布局
ctrl+l 垂直布局
```