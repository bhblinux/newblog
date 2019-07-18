---
title: "Java基础"
date: 2018-07-28T13:16:19+08:00
categories:
-   编程
tags:
-   java
---

# Table of Contents

1.  [基础](#org0b75d95)
    1.  [数据类型](#org97c4200)
2.  [面向对象](#orge9e0f6d)
    1.  [封装](#org2c76a3f)
    2.  [继承](#org7bad58d)
    3.  [多态](#org6d3292d)
    4.  [String类](#org416b302)
3.  [设计模式](#org4b0d00a)
    1.  [共享设计模式](#orga1cbfb6)
    2.  [关系映射](#org8d73915)
    3.  [代码块](#org3885c00)



[ org-mode](https://www.cnblogs.com/holbrook/archive/2012/04/12/2444992.html)


<a id="org0b75d95"></a>

# 基础


<a id="org97c4200"></a>

## 数据类型     :重点:

选择数据类型原则 

1.  整数用 `int` 小数用 `double`
2.  如果描述日期数字或者表示文件大小使用 `long`
3.  如果实现内容传递或者是编码转换使用 =byte =

---


<a id="orge9e0f6d"></a>

# 面向对象


<a id="org2c76a3f"></a>

## 封装

构造方法

1.  在 `new`  的时候只会被调用一次
2.  普通方法可以随意调用

匿名对象

-   没有名字的对象,能用一次

---


<a id="org7bad58d"></a>

## 继承

---


<a id="org6d3292d"></a>

## 多态

---


<a id="org416b302"></a>

## String类

相关方法
构造方法 

-   **String(byte[] bytes):** 将字符数组转成字符串
-   String(byte[] bytes, int offset, int length)
-   **String(byte[] bytes) :** 通过使用平台的默认字符集解码指定的 byte 数组，构造一个新的 String。
-   **String(byte[] bytes, Charset charset) :** 通过使用指定的 charset 解码指定的 byte 数组，构造一个新的 String。
-   **String(byte[] bytes, int offset, int length) :** 通过使用平台的默认字符集解码指定的 byte 子数组，构造一个新的 String。
-   **String(byte[] bytes, int offset, int length, Charset charset) :** 通过使用指定的 charset 解码指定的 byte 子数组，构造一个新的 String。

普通方法

-   **char charAt(int index):** 返回指定索引处的 char 值。
-   **char[] toCharArray() :** 将此字符串转换为一个新的字符数组。
-   **getBytes() :** 根据平台默认字符集,返回字节数组
-   **getBytes(String charsetName) :** 根据指定的字符编码转成字节数组
-   **length() :** 返回此字符串的长度。
-   **toLowerCase() :** 字符串转小写
-   **toUpperCase() :** 转大写
-   **trim() :** 去掉首尾空格
-   **replace(char oldChar, char newChar) :** 返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。
-   **isEmpty() :** 当且仅当 length() 为 0 时返回 true。
-   **equals(Object anObject) :** 将此字符串与指定的对象比较。
-   **equalsIgnoreCase(String anotherString) :** 不区分大小写比较
-   **int compareTo(String anotherString) :** 按照字符编码比较, 0 比较内容相等 ; >0 大于;<0  小于;
-   **contains(CharSequence s) :** 当且仅当此字符串包含指定的 char 值序列时，返回 true。
-   **endsWith(String suffix) :** 测试此字符串是否以指定的后缀结束。
-   **startsWith(String prefix) :** 测试此字符串是否以指定的前缀开始。
-   **startsWith(String prefix, int toffset) :** 测试此字符串从指定索引开始的子字符串是否以指定前缀开始。
-   **indexOf(int ch) :** 返回指定字符在此字符串中第一次出现处的索引。


<a id="org4b0d00a"></a>

# 设计模式


<a id="orga1cbfb6"></a>

## 共享设计模式


<a id="org8d73915"></a>

## 关系映射

1.  正常类编写
2.  设置关联字段(引用)
3.  一对多 的用的数组 反之
4.  代码测试
    -   设置数据
    -   取出数据


<a id="org3885c00"></a>

## 代码块

*尽可能在写代码的过程中不要去使用代码块* 

-   普通代码块
-   构造代码块
    -   将{}写在类中
-   静态代码块
-   同步代码块

