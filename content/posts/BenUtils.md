---
title: BenUtils
author: Bhblinux
tags:
  - java
categories:
  - 编程
date: 2018-04-16 21:30:00
---
# 内省
```java
package com.yonyou.demo01;

import java.beans.BeanInfo;
import java.beans.IntrospectionException;
import java.beans.Introspector;
import java.beans.PropertyDescriptor;
import java.lang.reflect.Method;

import org.junit.Test;

public class test01 {
	public static void main(String[] args) {
		
	}
	@Test
	public void getAllProperty() throws Exception{
		//Introspector 内省类
		BeanInfo beanInfo = Introspector.getBeanInfo(Person.class);
		//获取所有属性描述器
		PropertyDescriptor[] propertyDescriptors = beanInfo.getPropertyDescriptors();
		for (PropertyDescriptor propertyDescriptor : propertyDescriptors) {
			System.out.println(propertyDescriptor.getReadMethod()); //get方法
			
		}
	}
	@Test
	public  void testProperty() throws Exception{
		Person p=new Person();
		//属性描述器
		PropertyDescriptor propertyDescriptor = new PropertyDescriptor("name", Person.class);
		//获取属性对应的get或set方法设置或者获取属性
		Method writeMethod = propertyDescriptor.getWriteMethod();
		//执行改方法设置属性值
		writeMethod.invoke(p, "zhansan");
		System.out.println(p.name);
	}
}


class Person{
	
	int age;
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	String name;
}
```
# jar包
```java
commons-beanutils-1.8.0.jar
commons-logging.jar
```

- javabean 类的权限必须是public

## setProperty

## 转化器
- ConvertUtils.register

```java
/*
 * BeanUtils 设置的属性值,必须要设置一个转化器
 */

package com.yonyou.demo01;



import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

import org.apache.commons.beanutils.BeanUtils;
import org.apache.commons.beanutils.ConvertUtils;
import org.apache.commons.beanutils.Converter;


public class test02 {
	public static void main(String[] args) throws Exception {
		String name="张三";
		String age="10";
		String birthday="2013-12-30";
		
		//注册类型转化器
		ConvertUtils.register(new Converter() {
			
			//参数一: 要转化的数据类型 参数二:参数的值
			@Override
			public Object convert(Class type, Object value) {
				SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
				Date data = null;
				try {
					data = simpleDateFormat.parse((String)value);
				} catch (ParseException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				return data;
			}
		}, Date.class);
		
		
		Emp1 e=new Emp1();
		BeanUtils.setProperty(e, "name", name);
		BeanUtils.setProperty(e, "age", age);
		BeanUtils.setProperty(e,"birthday", birthday);
		System.out.println(e.getBirthday());
	}
}


```