---
title: "Java_ref"
date: 2019-07-21T16:46:02+08:00
draft: true
tags: [java]
categories: [编程]
---

# 反射
----

## 类加载器  
当程序要使用某个类的时候,如果该类还未被加载到内存中,则系统会通过加载,连接,初始化来实现对这个类进行初始化.  
1. 加载  
    - 就是指将Class文件读入内存,并为之创建一个Class对象  
    - 任何类被使用时系统都会建立一个class对象  
2. 连接
    - 验证 是否有正确的内部结构，并和其他类协调一致  
    - 准备 负责为类的静态成员分配内存，并设置默认初始化值  
    - 解析 将类的二进制数据中的符号引用替换为直接引用  

3. 初始化 
    - 就是我们以前讲过的初始化步骤（new 对象）  
*注：简单的说就是：把.class文件加载到内存里，并把这个.class文件封装成一个Class类型的对象。*

## 类的加载机制  
以下的情况，会加载这个类。  
- 创建类的实例
- 类的静态变量，或者为静态变量赋值
- 类的静态方法
- 使用反射方式来强制创建某个类或接口对应的java.lang.Class对象
- 初始化某个类的子类
- 直接使用java.exe命令来运行某个主类


## 类加载器(了解)  
负责将.class文件加载到内在中，并为之生成对应的Class对象。  
- Bootstrap ClassLoader 根类加载器
    - 也被称为引导类加载器，负责Java核心类的加载
    - 比如System,String等。在JDK中JRE的lib目录下rt.jar文件中  
- Extension ClassLoader 扩展类加载器
    - 负责JRE的扩展目录中jar包的加载。
    - 在JDK中JRE的lib目录下ext目录
- System ClassLoader 系统类加载器
    - 负责在JVM启动时加载来自java命令的class文件，以及classpath环境变量所指定的jar包和类路径。
    - 我们用的是System ClassLoader 系统类加载器

## 反射  
### 反射定义  
JAVA反射机制是在运行状态中，
- 对于任意一个类，都能够知道这个类的所有属性和方法；
- 对于任意一个对象，都能够调用它的任意一个方法和属性；
- 这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。
- 反射技术
    - 条件：运行状态
	- 已知：一个类或一个对象(根本是已知.class文件)
	- 结果：得到这个类或对象的所有方法和属性


### Class类  
- Class类及Class对象的了解  
要想解剖一个类，必须先了解Class对象。
阅读API的Class类得知，Class 没有公共构造方法。Class 对象是在加载类时由 Java 虚拟机以及通过调用类加载器中的 defineClass 方法自动构造的。

- 得到Class对象
    1. 通过Object类中的getClass()方法
    ```java
    Person p =new Person();
    Class clazz=Persion.getClass();
    ```  
    2. 通过 类名.class 获取到字节码文件对象（任意数据类型都具备一个class静态属性,看上去要比第一种方式简单）。  
    ```java
    Class clazz = Person.class;
    ```  
    3. 通过Class类中的方法（将类名作为字符串传递给Class类中的静态方法forName即可）。  
    ```java
    Class c3 = Class.forName("Person");
    ```


#### 获取构造方法  
1. 得到无参构造方法  
*public Constructor<?>[] getConstructors()*  
获取所有的public,修饰的构造方法  
选择无参的构造方法,不见一使用   
*public Constructor<T> getConstructor(Class<?>... parameterTypes) *  
获取public修饰, 指定参数类型所对应的构造方法。  
不传参数得到无参构造方法  

2. 运行无参构造方法  
*public T newInstance(Object... initargs) *  
使用此 Constructor 对象表示的构造方法来创建该构造方法的声明类的新实例，并用指定的初始化参数初始化该实例。   
因为是无参构造，所以不传参数。  

3. 通过反射获取无参构造方法并使用的代码演示：  


```java
package com.yonyou.ref;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.sql.Connection;

public class demo01 {

	public static void main(String[] args) throws Exception {
		Class clazz = Class.forName("com.yonyou.ref.Person");
//		Constructor[] constructors = clazz.getConstructors();
		//获取所有构造方法
//		for (Constructor constructor : constructors) {
//			System.out.println(constructor);
//		}
		//获取指定的构造方法,空参数的构造方法
		Constructor c = clazz.getConstructor();
		System.out.println(c);
		//创建实例对象
		Object obj = c.newInstance();
		System.out.println(obj);
	}
}

```


#### 通过反射获取有参构造方法并使用

1. 得到有参的构造方法  
*public Constructor<T> getConstructor(Class<?>... parameterTypes) *  
获取public修饰, 指定参数类型所对应的构造方法。  
传相应的参数类型得到有参构造方法。  

2. 运行无参构造方法  
*public T newInstance(Object... initargs) *  
使用此 Constructor 对象表示的构造方法来创建该构造方法的声明类的新实例，并用指定的初始化参数初始化该实例。   
因为是有参构造，所以传相应的参数值。  

3. 通过反射获取有参构造方法并使用的代码演示： 

```java
package com.yonyou.ref;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.sql.Connection;

public class demo01 {

	public static void main(String[] args) throws Exception {
		Class clazz = Class.forName("com.yonyou.ref.Person");
		Constructor c = clazz.getConstructor(String.class,int.class);
		Object newInstance = c.newInstance("张三",20);
		System.out.println(newInstance);
	}
}


```

4. 通过反射获取私有构造方法并使用  
    - 得到私有的构造方法
    *public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes) *  
	 获取指定参数类型所对应的构造方法(包含私有的)。  
	*public Constructor<?>[] getDeclaredConstructors() *			  
    获取所有的构造方法(包含私有的)。  
    - 运行私有构造方法  
    *public void setAccessible(boolean flag)*  
    将此对象的 accessible 标志设置为指示的布尔值。  
    设置为true,这个方法保证我们得到的私有构造方法的运行。（取消运行时期的权限检查。）  
    *public T newInstance(Object... initargs) *  
    使用此 Constructor 对象表示的构造方法来创建该构造方法的声明类的新实例，并用指定的初始化参数初始化该实例。 
    - 通过反射获取私有构造方法并使用的代码演示  

    ```java
    package com.yonyou.ref;

    import java.lang.reflect.Constructor;
    import java.lang.reflect.InvocationTargetException;
    import java.sql.Connection;

    public class demo01 {

        public static void main(String[] args) throws Exception {
            Class clazz = Class.forName("com.yonyou.ref.Person");
            //获取次有构造函数
            Constructor dc = clazz.getDeclaredConstructor(String.class);
            dc.setAccessible(true);
            Object obj = dc.newInstance("li");
            System.out.println(obj);
        }
    }

    ```


5. 反射获取成员变量并改值  
    - 获取成员变量
        - 得到公共的成员变量   
        *public Field getField(String name)*   
        返回一个 Field 对象，它反映此 Class 对象所表示的类或接口的指定公共成员字段。   
        *public Field[]getFields()*        
        返回一个包含某些 Field 对象的数组，这些对象反映此 Class 对象所表示的类或接口的所有可访问公共字段。      
        *public Field[] getDeclaredFields()*    
        返回 Field 对象的一个数组，这些对象反映此 Class 对象所表示的类或接口所声明的所有字段。    



    - 修改成员变量(Field)的值    
        - 修改成员变量(Field)的值    
        *public void set(Object obj, Object value)*   
        将指定对象变量上此 Field 对象表示的字段设置为指定的新值。   
        obj指的是修改的是那个对象的这个成员变量值。  

    - 反射获取成员变量并改值的代码演示 

    ```java
    package com.yonyou.ref;
    import java.lang.reflect.Constructor;
    import java.lang.reflect.Field;
    import java.lang.reflect.InvocationTargetException;
    import java.sql.Connection;
    public class demo01 {
        public static void main(String[] args) throws Exception {
            Class clazz = Class.forName("com.yonyou.ref.Person");
            //获取次有构造函数
            Constructor constructor = clazz.getConstructor();
            Object o= constructor.newInstance();
    //		Field[] fields = clazz.getFields();
    //		for (Field field : fields) {
    //			System.out.println(field);
    //			
    //		}
            //获取私有的成员
    //		Field[] declaredFields = clazz.getDeclaredFields();
    //		for (Field field : declaredFields) {
    //			System.out.println(field);
    //		}
            Field filed = clazz.getDeclaredField("name");
            filed.setAccessible(true);
            filed.set(o, "liang");
            System.out.println(o);
        }
    }
    ```


6. 反射获取空参数成员方法并运行      
    - 获取空参数成员方法
        *public Method getMethod(String name, Class<?>... parameterTypes)*   
        返回一个 Method 对象，它反映此 Class 对象所表示的类或接口的指定公共成员方法。 
        *public Method[] getMethods()*     
        返回一个包含某些 Method 对象的数组，这些对象反映此      
        Class对象所表示的类或接口（包括那些由该类或接口声明的以及从超类和超接口继承的那些的类或接口）的公共 member 方法。    
        得到全部的成员方法(包括私有的，如果要使用私有成员方法，要先进行public void setAccessible(boolean flag) 设置。)    
        *public Method getDeclaredMethod(String name, Class<?>... parameterTypes)*   
        返回一个 Method 对象，该对象反映此 Class 对象所表示的类或接口的指定已声明方法。    
        *public Method[] getDeclaredMethods()*   
        返回 Method 对象的一个数组，这些对象反映此 Class    
        对象表示的类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。 



    - 使用Method方法对象
    *public Object invoke(Object obj, Object... args)*    对带有指定参数的指定对象调用由此 Method 对象表示的底层方法。    
	obj 指的是调这个方法的对象。   
	args 指的是调用这个方法所要用到的参数列表。   
	返回值Object就是方法的返回对象。如果方法没有返回值 ，返回的是null.   

    - 反射获取空参数成员方法并运行代码演示