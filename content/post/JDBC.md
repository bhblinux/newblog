---
title: JDBC
author: Bhblinux
tags:
  - jdbc
categories:
  - 数据库
date: 2018-05-12 19:57:00
---
## 连接
1. 导包 mysql-connector-java-5.1.7-bin.jar
2.  连接高版本mysql数据库，而驱动版本比较低则需要凑单jdbcurl中加入 字符集 jdbc:mysql:///day06?characterEncoding=utf-8
- 方法一

``` java
package com.yonyou;

import java.sql.Connection;
import java.sql.Driver;
import java.sql.SQLException;
import java.util.Properties;
import org.junit.Test;
/*
 * jdbc数据连接
 * 导包 mysql-connector-java-5.1.7-bin.jar
 */
public class demo01 {
	//连接数据url
	private String url="jdbc:mysql://localhost:3306/day0508";
	@Test
	public void test01(){
		//1.创建驱动程序对象
		Driver driver=null;
		Properties properties = new Properties();
		properties.setProperty("user", "root");
		properties.setProperty("password", "admin");
		//2.连接数据库
		try {
			driver=new com.mysql.jdbc.Driver();
			Connection connect = driver.connect(url, properties);
			System.out.println(connect);
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
}

```

- 方法二

```java
@Test
	public void test03(){
		try { 
			Class.forName("com.mysql.jdbc.Driver"); 
			Connection connection = DriverManager.getConnection(url,"root","admin");
			System.out.println(connection);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
	}
```

##  jdbc接口核心api
### Connection接口 表示java程序和数据库的连接对象
1. createStatement() 创建Statement 对象
- prepareStatement(String sql) 创建PreparedStatement对象
- CallableStatement prepareCall(String sql) 创建CallableStatement对象

###  Statement接口： 用于执行静态的sql语句
1.  int executeUpdate(String sql)  ： 执行静态的更新sql语句（DDL，DML）
2. ResultSet executeQuery(String sql)  ：执行的静态的查询sql语句（DQL）

### PreparedStatement接口：用于执行预编译sql语句
1. int executeUpdate() ： 执行预编译的更新sql语句（DDL，DML）
- ResultSet executeQuery()  ： 执行预编译的查询sql语句（DQL）

### CallableStatement接口：用于执行存储过程的sql语句（call xxx）
1. ResultSet executeQuery()  ： 调用存储过程的方法

### ResultSet接口：用于封装查询出来的数据
1. boolean next() ： 将光标移动到下一行
2. getXX() : 获取列的值


## DDL操作
1. 创建表

```java
package com.yonyou;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

import org.junit.Test;

/*
 * statement 执行sql语句
 */
public class demo02 {
	private String url="jdbc:mysql://127.0.0.1:3306/day0508";
	private String user="root";
	private String password="admin";
	@Test
	public void test01(){
		Connection connection=null;
		Statement createStatement=null;
		try {
			//驱动注册
			Class.forName("com.mysql.jdbc.Driver");
			//2获取连接
			connection = DriverManager.getConnection(url, user, password);
			//3.创建statement对象
			createStatement = connection.createStatement();
			//4 准备sql
			String sql="CREATE TABLE test_log2 (id INT PRIMARY KEY AUTO_INCREMENT,content VARCHAR(100));";
			int executeUpdate = createStatement.executeUpdate(sql);
			System.out.println(executeUpdate);
			
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally{
			//关闭链接
			try {
				if(createStatement!=null){
					createStatement.close();
				}
				
			} catch (SQLException e) {
				e.printStackTrace();
			}
			try {
				if(connection!=null){
					connection.close();
				}
				
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
}

/*
 * 增加
 */
package com.yonyou;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

import org.junit.Test;

public class demo03 {

	
	/*
	 * 增加
	 */
	private String url="jdbc:mysql://127.0.0.1:3306/day0508";
	private String user="root";
	private String password="admin";
	Connection connection=null;
	Statement createStatement=null;
	
	@Test
	public void test01(){
		try {
			Class.forName("com.mysql.jdbc.Driver");
			 connection = DriverManager.getConnection(url, user, password);
			 createStatement = connection.createStatement();
			String sql="insert into dept values(5,'bb') ";
			createStatement.executeUpdate(sql);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally{
			if(createStatement!=null){
				try {
					createStatement.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			if(connection!=null){
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
		
	}
	
	
	// 更新
	
	@Test
	public void test02(){
		try {
			Class.forName("com.mysql.jdbc.Driver");
			 connection = DriverManager.getConnection(url, user, password);
			 createStatement = connection.createStatement();
			String sql="UPDATE dept SET deptname='dd' WHERE id =5 ";
			createStatement.executeUpdate(sql);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally{
			if(createStatement!=null){
				try {
					createStatement.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			if(connection!=null){
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
		
	}
	
}

//删除
	@Test
	public void test03(){
		Connection connection2 = JdbcUtils.getConnection();
		Statement createStatement2=null;
		try {
			 createStatement2 = connection2.createStatement();
			 String sql="delete from dept where id =4";
			 createStatement2.executeUpdate(sql);
			 JdbcUtils.relse(connection2, createStatement2);
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
    
    封装工具包
    package com.yonyou;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;



public class JdbcUtils {
	private static String url="jdbc:mysql://127.0.0.1:3306/day0508";
	private static String user="root";
	private static String password="admin";
	static{
		try {
			Class.forName("com.mysql.jdbc.Driver");
			
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
	
	//获取连接
	public static Connection getConnection(){
		Connection connection=null;
		try {
			 connection = DriverManager.getConnection(url, user, password);
			
		} catch (SQLException e ) {
			e.printStackTrace();
		}
		return connection;
	}
	
	//释放资源
	public static void relse(Connection conn,Statement createStatement2){
		if(createStatement2!=null){
			try {
				createStatement2.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		if(conn!=null){
			try {
				conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
}

```

## DQL 查询

1. ResultSet 结果集

```java
/*
 * 查询
 */
package com.yonyou;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

import org.junit.Test;

public class demo04 {

	
	
	@Test
	public void demo01(){
		Connection connection=null; 
		Statement sta=null;
		
		try {
			connection= JdbcUtils.getConnection();
			sta = connection.createStatement();
			String sql="select * from dept";
			ResultSet rs = sta.executeQuery(sql);
			//boolean flag=rs.next();
//			if(flag){
//				//根据列的索引取列的值
////				int id = rs.getInt(1);
////				String name=rs.getString(2);
////				System.out.println(id);
////				System.out.println(name);
//				//根据列明获取 不区分大小写
//				int id=rs.getInt("id");
//				System.out.println(id);
//			}
			
			while(rs.next()){
				int id=rs.getInt(1);
				System.out.println(id+":"+rs.getString(2));
			}
			
			
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			JdbcUtils.relse(connection, sta);
		}
	}
}

```

## 防注入预编译sql
```java
package com.yonyou;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.Statement;

import org.junit.Test;

public class demo05 {

	@Test
	public void test01(){
		Connection conn=null;
		PreparedStatement stat=null;
		try {
			conn = JdbcUtils.getConnection();
			
			String sql="insert into dept values(?,?)";
			//预编译
			stat=conn.prepareStatement(sql);
			//4设置参数的值
			stat.setInt(1, 4);
			stat.setString(2, "哈哈");
			//执行sql
			stat.executeUpdate();
			
		} catch (Exception e) {
			// TODO: handle exception
		}
		
		
	}
}

```

## 执行存储过程
- callablestatement

## 类路径
- demo05.class.getResourceAsStream("/")
- / 表示classpath根目录
- java项目下 classpath的根目录从bin目录开始
- web项目下,classpath的根目录从web-inf/calsses目录下


## jdbc批处理

- 方式一

```java
Statement.addBatch(sql)
executeBatch() 执行批处理
clearBatch 清除批处理命令
```

- 方式二

```java
ps=conn.preparedstatement(sql)
prepareStatement.addBatch()
prepareStatement.executeBatch()
clearBatch
```
## 事物
- 数据库开机事物命令
1. start transaction 开启事物
2. Rollback 回滚事物
3. Commit 提交事物
- jdbc开启事物
1. conn.setAutoCommit(false)
2. conn.commit();
3. conn.rollback();


## 数据库的隔离级别
- ~Read uncommitted：如果将数据库设定为此隔离级别，数据库将会有脏读、不可重复度、幻读的问题。
- ~Read committed：如果将数据库设定为此隔离级别，数据库可以防止脏读，但有不可重复度、幻读的问题。
- 	~Repeatable read: 如果将数据库设定为此隔离级别，数据库可以防止脏读、不可重复度，但是不能防止幻读。
- 	~Serializable：将数据库串行化,可以避免脏读、不可重复读、幻读。

## 锁
- 更新丢失
- 悲观锁
	- 悲观锁悲观的认为每次操作都会造成更新丢失问题,在每次查询的时候加上更新锁
    - select * from ... for update

- 乐观锁
	- 乐观锁,认为每次查询不会造成更新丢失,给表加一个版本字段	
    - update order set stat=1 and version=version+1 where id =1 and version=0;

## 连接池
- DBCP tomcat默认支持
- 导包
	- commons-pool-1.5.6.jar
    - commons-dbcp-1.4.jar
    
```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

import org.apache.commons.dbcp.BasicDataSource;
import org.junit.Test;

/*
 * dbcp
 */
public class demo01 {

	 @Test
	 public void dbcpDemo01(){
		 Connection conn=null;
		 PreparedStatement ps=null;
		 ResultSet rs=null;
		 BasicDataSource bs=new BasicDataSource();
		 bs.setDriverClassName("com.mysql.jdbc.Driver");
		 bs.setUrl("jdbc:mysql:///day0508");
		 bs.setUsername("root");
		 bs.setPassword("admin");
		 try {
			conn=bs.getConnection();
			
			ps=conn.prepareStatement("select * from dept");
			rs=ps.executeQuery();
			while(rs.next()){
				int id=rs.getInt(1);
				String name=rs.getString(2);
				System.out.println(id+":"+name);
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	 }
}

```
- C3P0
	- 导包 c3p0-0.9.1.2.jar
    
```java
import java.beans.PropertyVetoException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

import org.apache.commons.dbcp.BasicDataSource;
import org.junit.Test;

import com.mchange.v2.c3p0.ComboPooledDataSource;

/*
 * dbcp
 */
public class demo02 {

	 @Test
	 public void dbcpDemo01(){
		 Connection conn=null;
		 PreparedStatement ps=null;
		 ResultSet rs=null;
		 
		 ComboPooledDataSource cbds=new ComboPooledDataSource();
		 try {
			 cbds.setDriverClass("com.mysql.jdbc.Driver");
			 cbds.setJdbcUrl("jdbc:mysql:///day0508");
			 cbds.setUser("root");
			 cbds.setPassword("admin");
			 conn=cbds.getConnection();
			ps=conn.prepareStatement("select * from dept");
			rs=ps.executeQuery();
			while(rs.next()){
				int id=rs.getInt(1);
				String name=rs.getString(2);
				System.out.println(id+":"+name);
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	 }
}


```
- javax.sql 接口 DataSource

- tomcat配置数据连接池
	- 如何为tomcat配置数据源
    - ~tomcat/conf/context.xml文件中配置<Context>配置在这个位置的信息将会被所有的web应用所共享
    - ~tomcat/conf/[engin]/[Host]/context.xml文件中可以配置<Context>标签,这里配置的信息将会被这台虚拟主机中的所有web应用所共享
    - ~tomcat/conf/server.xml文件中的<Host>标签中配置<Context>标签,这是web应用的第一种配置方式,在这个标签中配置的信息将只对当前web应用起作用
    - ~tomcat/conf/[engin]/[Host]/自己创建一个.xml文件,在这个文件中使用<Context>标签配置一个web应用,这是web应用第二种配置方式,在这个<Context>标签中配置的信息将只会对当前web应用起作用
 	 - ~web应用还有第三种配置方式:将web应用直接放置到虚拟主机管理的目录.此时可以在web应用的META-INF文件夹下创建一个context.xml文件,在其中可以写<Context>标签进行配置,这种配置信息将只会对当前web应用起作用


```java
在~tomcat/conf/server.xml

<Context path="/INFO_DMS_WEB" docBase="INFO_DMS_WEB" debug="0" reloadable="false">
           <Resource name="jdbc/infodms" auth="Container" type="javax.sql.DataSource"
               maxActive="100" maxIdle="30" maxWait="60000"
               username="infodms" password="dms" driverClassName="com.ibm.db2.jcc.DB2Driver"
               url="jdbc:db2://localhost:50000/INFODMS"/>
        </Context> 
	<Context docBase="Carenet_ASC" path="/Carenet_ASC" reloadable="true" source="Carenet_ASC">
          <Resource name="jdbc/infoMonitor" auth="Container"
              type="javax.sql.DataSource" driverClassName="com.ibm.db2.jcc.DB2Driver"             
              url="jdbc:db2://localhost:50000/infodms"                    
              username="infodms" password="dms" maxActive="20" maxIdle="10" SetBigStringTryClob="true"
              maxWait="-1"/>
	 </Context>
```



```java
<Resource name="mySource"
					    auth="Container"
					    type="javax.sql.DataSource"
					    username="root"
					    password="root"
					    driverClassName="com.mysql.jdbc.Driver"
					    url="jdbc:mysql:///day11"
					    maxActive="8"
					    maxIdle="4"/>  
 ```
- 如果在程序中获取这个数据源

```java
配置文件 META-INF/context.xml
<?xml version="1.0" encoding="UTF-8"?>

<Context>
	<Resource name="mySource"
		auth="zuliang"
		type="javax.sql.DataSource"
		username="root"
		password="admin"
		driverClassName="com.mysql.jdbc.Driver"
		url="jdbc:mysql:///day0508"
		 maxActive="8"
		 maxIdle="4"
	/>
	

</Context>


tomcat使用jdbc数据源


想要访问jndi就必须在Servlet中才能执行下列代码:

				Context initCtx = new InitialContext();
				Context jndi = (Context) initCtx.lookup("java:comp/env");
				DataSource source = jndi.lookUp("mySource");
```


```java
package com.yonyou.demo;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

import javax.naming.Context;
import javax.naming.InitialContext;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.sql.DataSource;


public class DataSoucerDemo01 extends HttpServlet {

	/**
	 * The doGet method of the servlet. <br>
	 *
	 * This method is called when a form has its tag value method equals to get.
	 * 
	 * @param request the request send by the client to the server
	 * @param response the response send by the server to the client
	 * @throws ServletException if an error occurred
	 * @throws IOException if an error occurred
	 */
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

	}

	public void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		
	}
	@Override
	public void init() throws ServletException {
		try {
			InitialContext initialContext = new InitialContext();
			Context jndi = (Context)initialContext.lookup("java:comp/env");
			DataSource source=(DataSource) jndi.lookup("mySource");
			Connection conn = source.getConnection();
			PreparedStatement ps = conn.prepareStatement("select * from dept");
			ResultSet rs = ps.executeQuery();
			while(rs.next()){
				int id = rs.getInt(1);
				String name = rs.getString(2);
				System.out.println(id+":"+name);
			}
			
		} catch (Exception e) {
			// TODO: handle exception
		}
	}

}

```
# 动态代理设计模式
- java.lang.reflect 类 Proxy
- newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)