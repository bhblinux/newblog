---
title: JSP
author: Bhblinux
tags:
  - java
categories:
  - 编程
date: 2018-05-01 15:41:00
---
## 特点

1. jsp运行必须交给tomcat服务器 ,tomcat的work目录,存放jsp运行时的临时文件

## 执行过程

1. tomcat扫描jsp文件在work目录包jsp文件翻译成java源文件.
2. tomcat服务器会把java源文件编程成字节码文件
3. 返回构造类对象
4. 调用相应方法

## jsp语法

1. jsp模板
2. 表达式

```java

<%=变量或表达式%>
输出表达式的结果或值



```
3. 脚本

```java
<% java 代码 %>
```

4. 声明
5. 注释
6. jsp指令
  - include
  
  原理是吧被包含的页面内容反映到包含页面中,合并成翻译成一个java源文件,再编译运行,这种包含叫静态包含
  
  如果使用静态包含,被包含页面中不需要出现全局的html标签

  ```java
  <%@include file="" %>
  ```
  
  - taglib
  - page
  
  告诉tomcat服务器如何翻译jsp文件

  ```java
	<%@ page
    language="java" 用java语言翻译
    import "java.util.*" 导包,多个用逗号分隔
    pageEncodeing="utf-8" 使用服务器用什么编码翻译
    contentType="text/html;charset=utf-8" 服务器发送浏览器的数据类型和编码
    errorPage="error.jsp"
    isErrorPage="false"
    buffer="8kb"
    session="true"
    isELIgnored="false"
    
    %>

  ```
  
 7. taglib 指令
 
 ### jsp内置对象
 
 1.  request HttpServletRequest
 - response HttpServletResponse
 - config ServletConfig
 - application ServletContext
 	
 - session HttpSeesion
 - exception Throwable
 - out JspWriter
 
 	带缓冲区的输出流,默认是8kb,手动刷新缓冲区out.flush();关闭缓冲区buffer="0kb",查看剩余缓冲区的大小out.getRemaining();,查看现在缓冲区的大小,out.getBufferSize();
 
 - page Object(this)
 - pageContext PageContext
 	jsp的上下文对象,把8个经常使用的内置对象封装到pageContext对象中,可以向四个域对象保存数据,域范围常量PageContext.PAGE_SCOPE,PageContext.REQUEST_SCOPE,PageContext.SEXXION_SCOPE,PageContext.APPLICATION_SCOPE,
    - 自动搜索.pageContext.findAttribute("str");
    - 顺序page,request,session,context(application)
    
    
## 域对象

   1. page域(pageContext):只能的当前jsp页面中使用()
   - request域(reqeust):只能在同一个请求中使用
   - sessiion域(session):只能在一个会话中使用
   - context域(servletContext):只能在同一个web应用中使用
   
## EL表达式

- 替换jsp表达式
- 作用:向浏览器输出域对象中的变量或表达式计算结果

```JAVA
//基本使用
<% String name="rose"; pageContext.setAttribute("name",name,PageContext.REQUEST_SCOPE);
%>
    <%= name %>
    <!-- EL表达式 
    PAGE_SCOPE:${name }-->
    <%--从制定的域中获取数据 --%>
    ${requestScope.name  }

```

### 语法
1. 基本数据类型
	1. 获取 ${name}
    - 指定域获取 ${requestScope.name}
	- 获取自定对象的属性 ${student.name }
    - 获取list ${list[0].name }- ${list[0].age }
    - map ${map['100'].name }
    - 判断字符串是空或者为null ${empty name }
    
## jsp标签
- 替换jsp脚本
- 流程判断
- 跳转页面(转发,重定向)

1. 内置标签(动作标签) 不需要在jsp页面导入标签
2. jstl(标签) 需要导包
3. 自定义标签

### 动作标签
1. jsp:forward 转发
2. jsp:pararm 参数
3. jsp.include 包含
	- 包含于被包含的页面,先各自翻译成java源文件,然后运行时合并在一起
    -  该动态包含可以传参
4. jsp:useBean 创建对象 id calss 
5. jsp:setProperty 赋值 property="name" name="stu" value="jxx"
6. jsp.getProperty 获取 property="name" name="stu"

### jstl
1. 核心标签库(c标签库)
- 国际化便签库(fmt)
- el函数库 (fn)
- xml标签库 (x)
- sql标签库 (sql)

#### 使用
1. 导入标签库
	- 使用taglib使用标签库 @taglib uri="tld文件的uri名称"prefix="简写"
	-  tld文件中的uri内容 @taglib uri="http://java.sun.com/jsp/jstl/core"prefix="c" 
	- prefix tld文件中的short-name
- 使用
```java
    <%-- 使用标签 --%>
    保存数据
    <c:set></c:set>
    <%-- set标签:保存数据默认会保存到page域中 --%>
    <c:set var="name" value="rose"></c:set>
    获取数据
    <c:out></c:out>
    out 获取数据 
   	default 当value的值为null时,使用默认值,
   	escapeXml true 会转义标签标签 false 不转义默认true
   <c:out value="${name}" default="默认值"></c:out>
   
    单条件判断 test里可以写el表达式
    <c:if test=""></c:if>
    <c:if test="${10>5 }">条件成立</c:if>
    
    多条件判断
    <c:choose></c:choose>
    <c:when test=""></c:when>
    <c:otherwise></c:otherwise>
    <c:choose>
      <c:when test="${score>=80 && score<=100}">优秀</c:when>
      <c:when test="${score>=60 && score<80}">良好</c:when>
      <c:otherwise>不及格</c:otherwise>
    </c:choose>
    循环
    <c:forEach></c:forEach>
    <%-- <c:forEach 
    begin=""  从哪个开始
    end=""    哪个结束
    step=""   步长
    items=""  需要遍历的数据集合
    var=""    每个元素的名称
    varStatus=""  正在变量元素的状态对象(count属性,当前位置,从1开始
    ></c:forEach> 
    --%>
    <c:forEach begin="0" end="2" step="1" items="${list}" var="stu" varStatus="varSta">
     	序号:${varSta.count}---${stu.name }<br/>
     </c:forEach>
    <c:forTokens item="" delims=""></c:forTokens>
    <%-- forToken 标签  循环特殊的字符串--%>
	<c:set var="str" value="java-php-c"></c:set>
	<c:forTokens items="${str}" delims="-" var="s">
		${s }
	</c:forTokens>
    重定向
    <c:redirect></c:redirect>
    <c:redirect url="http://www.baidu.com"></c:redirect>
```
### 自定义标签

1. 先写标签处理器类,继承SimpleTagSupport

```java
package com.yonyou.mytag;

/*
 * 自定义标签
 */
import java.io.IOException;

import javax.servlet.ServletRequest;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.jsp.JspContext;
import javax.servlet.jsp.JspException;
import javax.servlet.jsp.JspWriter;
import javax.servlet.jsp.PageContext;
import javax.servlet.jsp.tagext.SimpleTagSupport;

//1.继承SimpleTagSupport
public class ShowIp extends SimpleTagSupport{
	
	//2.覆盖doTag方法
	@Override
	public void doTag() throws JspException, IOException {
		//向浏览器输出客户的ip地址
		PageContext pageContext=(PageContext) PageContext pageContext=(PageContext) this.getJspContext();;
		HttpServletRequest request = (HttpServletRequest) pageContext.getRequest();
		String remoteHost = request.getRemoteHost();
		JspWriter out = pageContext.getOut();
		out.write(remoteHost);
		
	}
}


```
2. 声明标签,在web-inf目录下面建立x.tld文件

```java
<?xml version="1.0" encoding="UTF-8" ?>

<taglib xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-jsptaglibrary_2_1.xsd"
    version="2.1">
    

  <tlib-version>1.1</tlib-version>
  <short-name>yonyou</short-name>
  <uri>http://yonyou.com</uri>

	<tag>
		<name>showIp</name>
		<tag-class>com.yonyou.mytag.ShowIp</tag-class>
		<body-content>scriptless</body-content>
	</tag>
</taglib>

```

3. 导入标签,并使用

```java
<%@taglib uri="http://yonyou.com" prefix="yonyou" %>
<yonyou:showIp></yonyou:showIp>

```

4. 自定义标签处理器类的声明周期

	1. setJspContext(JspContext pc),设置pageContext对象
    - setParent(JspTag parent) 设置父标签对象,传入父标签对象,如果没有父标签,则不调用子方法,通过getParent()方法得到父标签对象
    - setJspBody(JspFragmnent jspBody) 设置标签体内容,标签体内容封装到jspFragment对象,然后传入jspFragment对象,通过getjspbody()方法得到标签体内容
    - doTag()执行标签是调用
    
    
    
```java
	ppackage com.yonyou.mytag;

import java.io.IOException;
import java.io.StringWriter;

import javax.servlet.jsp.JspContext;
import javax.servlet.jsp.JspException;
import javax.servlet.jsp.JspWriter;
import javax.servlet.jsp.SkipPageException;
import javax.servlet.jsp.tagext.JspFragment;
import javax.servlet.jsp.tagext.SimpleTagSupport;

public class ShowStr extends SimpleTagSupport {

	@Override
	public void doTag() throws JspException, IOException {
	
		//得到标签体的内容
		JspFragment jspBody2 = this.getJspBody();
		JspContext jspContext2 = this.getJspContext();
		
		
		
		//输出标签体的内容
//		JspWriter out = jspContext2.getOut();
//		jspBody2.invoke(out);
//		jspBody2.invoke(null);//等价书面的代码
		//输出:什么都不做
		
		
		//控制重复执行
//		for(int i=0;i<4;i++){
//			jspBody2.invoke(null);
//		}
		
		//改变标签体的内容
		//1.创建一个StringWrite临时容器
		StringWriter stringWriter = new StringWriter();
		//把标签体的内容放置临时容器
		jspBody2.invoke(stringWriter);
		//3.获取容器的内容
		String string = stringWriter.toString();
		//4 改变内容
		String lowerCase = string.toLowerCase();
		//5.手动输出
		JspWriter out = jspContext2.getOut();
		out.write(lowerCase);
		
		
		//不输出:抛出SkipPageException异常
		
		
		throw new SkipPageException() ;
	
	}
	
}

```



