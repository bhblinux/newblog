---
title: servlet
author: Bhblinux
tags:
  - java
categories:
  - 编程
date: 2018-04-30 10:27:00
---
## 生命周期

1. 输入url,会在应用目录的web.xml中查找是否有匹配的url-pattern的内容
2. 如果找到匹配的url-pattern,则使用当前的servlet-name的名称到web.xml文件中查找是否相同的servlet配置,如果没有匹配到,则交给defalutservlet处理
3. 如果找到,则取出相对应的serlvert配置信息中的servlet-class 内容,然后通过反射创建对象,并调用相关方法

### 生命周期

servlet程序是有tomcat服务器控制

1. 构造方法:创建servlet对象的时候调用,第一次访问,servlet的时候创建servlet对象,会调用有参的,只调用1次,
2. init方法:创建完servlet对象的时候 ,只调用1次
3. service方法:每次发出请求时调用,每次请求调用一次
4. destroy:销毁serlvet对象的时候,停止服务器或者重新部署web应用的时候销毁servlet对象





### web.xml匹配规则

1. 精确匹配 /aa | /aa/bb
2. 模糊匹配  /* | *.后缀 \ *.do \
3. 默认的 / 缺省servlet 解析web应用的静态资源文件
4. 优先级 精确匹配 优先

## servlet自动加载

在servlet的配置文件中加上load-on-startup即可

### 域对象

#### HttpServletRequest

请求对象,获取请求信息

#### HttpServletResponse

响应对象,设置响应对象

#### ServletConfig

servlet配置对象

创建时机: 在创建完servlet对象后,在调用init方法之前创建

得到对象:直接从有参数的init方法得到

init-para要放在load-on-startup上边


```java
web.xml
<init-param>
 		<param-name>path</param-name>
 		<param-value>c:/1.txt</param-value>
 	</init-param>
    <!-- 让servlet自动加载 
    <load-on-startup>1</load-on-startup>-->
String initParameter = config.getInitParameter("path");
		response.getWriter().write(initParameter);

```

#### ServletContext

Servlet的上下文对象

```java

得到web应用路径:
	java.lang.String getContextPath()  
   rqeust.getContextPath() 等同于上面的代码
   
得到web应用参数
java.lang.String getInitParameter(java.lang.String name)  获取web应用的初始化参数
 java.util.Enumeration getInitParameterNames()  

域对象:
setAttribute(java.lang.String name, java.lang.Object object) 域对象有关的方法
 getAttribute(java.lang.String name) 
 removeAttribute(java.lang.String name) 
  
 转发:
  RequestDispatcher getRequestDispatcher(java.lang.String path)  转发(类似于重定向)
  request.getRequestDispatcher(路径).forward(reqeust,response);//等同于上面的代码
 
 java.lang.String getRealPath(java.lang.String path) 获取web应用资源文件
 String realPath = this.getServletContext().getRealPath("/");
 D:\Workspaces\jar包\apache-tomcat-6.0.39\webapps\day0430_web\
package com.yonyou;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Properties;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class demo03 extends HttpServlet {

	
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		Properties properties = new Properties();

		String realPath = this.getServletContext().getRealPath("/WEB-INF/classes/db.properties");
		properties.load(new FileInputStream(realPath));
		String property = properties.getProperty("name");
		System.out.println(property);
		
		
	}

}

//同上,返回一个字节了
getResourceAsStream(java.lang.String path) 


```

## 路径问题

```java
package com.yonyou;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class demo02 extends HttpServlet {

	
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		/* 关于路径问题 / 代表 
		 * 目标资源是个谁用:
		 * 1. 浏览器   / 代表tomcat安装目录下面的webapps的根目录下 
		 * 2. 服务器  / 代表当前web应用的根目录 webRoot目录下面
		 */
		response.setContentType("text/html;charset=utf-8");
		PrintWriter out = response.getWriter();
		/*
		 * 1.转发
		 */
		//request.getRequestDispatcher("/index.jsp").forward(request, response);
		//2 请求重定向
		//response.sendRedirect("/day0430_web/index.jsp");
		//3html页面的超链接href
		//out.write("<html><body><a href='/day0430_web/index.jsp'>aaa</a></body></html>");
		//4 html页面中的form提交地址
		out.write("<html><body><form action='/day0430_web/servlet/demo01' ><input type='submit'></form></body></html>");
		
	}

}

重定向是在客户端完成的，转发是在服务器完成的

```

## 会话管理

### cookie

数据会保存在客户端

1. 构造cookie 对象

Cookie(java.lang.String name, java.lang.String value) 

2. 设置cookie

void setPath(java.lang.String uri)  设置cookie的有效访问路径

1. 默认有效路径day0430_web目录下面,如果cookie不在有效路径下面则访问服务器资源的时候不会带cookie给服务器

setMaxAge(int expiry)  设置cookie的有效时间


1. 正整数  表示cookie数据保存在浏览器的缓存目录硬盘中
2. 负整数  保存在浏览器内存中 会话cookie
3. 0     删除同名的cookie数据     
4. 默认是秒
5. 浏览器一般只允许存放300个cookie,每个站点最多存放20个cookie,每个cookie的大小限制为4kb,不能保存中文

setValue(java.lang.String newValue) 设置cooike的值



3. 发送cookie到浏览器
HttpServletResponse  addCookie(Cookie cookie) 
4. 接受cookie
HttpServletRequest Cookie[] getCookies()  
	
 ```java
 package com.yonyou;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class CookieDemo01 extends HttpServlet {

	/*
	 * cooike
	 * (non-Javadoc)
	 * @see javax.servlet.http.HttpServlet#doGet(javax.servlet.http.HttpServletRequest, javax.servlet.http.HttpServletResponse)
	 */
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		response.setContentType("text/html;charset=utf-8");
		//1.创建cookie对象
		Cookie cookie = new Cookie("name","zuliang");
		Cookie cookie2 = new Cookie("age","27");
		//2. 将cookie发送到浏览器
		response.addCookie(cookie);
		response.addCookie(cookie2);
		
		//3 接受cookie信息
		Cookie[] cookies = request.getCookies();
		if(cookies!=null){
			for (Cookie cookie3 : cookies) {
				String name = cookie3.getName();
				System.out.println(name);
				
			}
		}
		
	}

}

 ```


### session

Interface HttpSession

数据会保存在服务端

1 创建或得到session

HttpServletRequest  

	HttpSession getSession() 
	HttpSession getSession(boolean create)  

2. 设置session对象

		setMaxInactiveInterval(int interval)   设置有效时间
        
        - 默认30分钟服务器自动回收
        
        全局修改session有效时间 以分钟session-config  session-timeout
	
        
    	invalidate() 销毁session对象
    	getId() 获取id
    	setAttribute(java.lang.String name, java.lang.Object value) 保存数据
        

 3. 获取session对象
 
 		getAttribute(java.lang.String name) 
		getAttributeNames() 

4. HttpSession session = request.getSession();解读

	1. 创建session对象,给session对象分配一个唯一id,叫JSESSIONID
    2. 把sessionid作为cooike的值发送给浏览器保存
    3. 第二次访问的时候,浏览器带着sessionid的cookie访问服务器
    4. 服务器看到sessionid,在发谁说器的内存中搜索是否存放对应的编号session对象
    5. 如果找到对应的编号,直接放回
    6. 如果找不到,则重新创建session对象
    ,
    7. 		String string = UUID.randomUUID().toString().replace("-", "");System.out.println(string);