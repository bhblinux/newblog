---
title: JSTL
author: Bhblinux
tags:
  - java
categories:
  - 编程
date: 2018-05-25 23:15:00
---
## 定义标签
### 传统标签

1. 写一个类实现tag接口
2. 写一个tld文件,描述写好的类
3. 在jsp页面引用tld文件并使用

### 简单标签
1. 写一个类实现SimpleTag接口(继承SimpleTag接口的默认实现类SimpleTagSupport)
2. 写一个tld文件,描述写好的类
3. 在jsp页面中引入tld文件,就可以在jsp页面中使用自定义标签了

```java

package com.yonyou;

import java.io.IOException;
import java.io.StringWriter;

import javax.servlet.jsp.JspException;
import javax.servlet.jsp.SkipPageException;
import javax.servlet.jsp.tagext.JspFragment;
import javax.servlet.jsp.tagext.SimpleTagSupport;

public class demo02 extends SimpleTagSupport {

	@Override
	public void doTag() throws JspException, IOException {
		/*
		控制标签体是否执行<c:if>
		//控制标签提不执行 ,什么都不做,就不执行
		 //控制标签执行*/
		
		//JspFragment jspBody2 = getJspBody();
		//jspBody2.invoke(getJspContext().getOut());
		//jspBody2.invoke(null);//null则跟上面一样
	        //控制标签之后的内容是否执行
		//什么在不做,标签后面的就会执行
		//控制便签后面的内容不执行抛出SkipPageException异常
		//throw new SkipPageException();
	        //控制标签体重复执行<c:foreach>
//		for(int i=0;i<4;i++){
//			getJspBody().invoke(null);
//		}
		
	       // 修改标签体后输出<c:out>
		StringWriter stringWriter = new StringWriter();
		JspFragment jspBody2 = getJspBody();
		jspBody2.invoke(stringWriter);
		String str=stringWriter.toString();
		str=str.toUpperCase();
		getJspContext().getOut().write(str);
		
        
	}
	
}

```

### 带属性的标签
1. 在标签处理类中增加一个javabean属性,这个属性就是要增加的标签的属性,并对外提供setXXX方法
2. 在tld文件中这个标签的描述中描述一下该属性  

```java
1.添加属性
private int times=0;
	public void setTimes(int times) {
		this.times = times;
	}
 2. tld添加属性
 <tag>
 	<name>demo02</name>
 	<tag-class>com.yonyou.demo02</tag-class>
 	<body-content>scriptless</body-content>
 	<attribute>
 		<name>times</name>
 		<required>true</required>
 		<rtexprvalue>true</rtexprvalue>
 		<type>int</type>
 	</attribute>
 </tag>
 3.使用
 //带属性的标签开发
		for(int i=0;i<times;i++){
			getJspBody().invoke(null);
		}
		
 4.调用
    <MyTag:demo02 times="4">aaa</MyTag:demo02>
```
