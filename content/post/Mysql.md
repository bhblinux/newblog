---
title: Mysql
author: Bhblinux
tags:
  - java
categories:
  - 编程
date: 2018-05-08 22:07:00
---
## 命令
### 数据库管理
- 链接数据库 
		mysql -u root -p  
        
- 查看数据库
		mysql> show databases;

- 创建数据库
		mysql> create database day0508;
     	mysql> create database day050801
 			-> default character set utf8;   
            
- 删除数据库
		mysql> drop database day050801;
        
- 查看数据库的默认字符集
		mysql> show create database day0508;
- 修改数据库的默认字符集
		mysql> alter database day0508 default character set gbk;
- 使用数据库
		mysql> use day0508;
### 表的操作
- 查看表
		mysql> show tables;
- 创建表
		mysql> create table student(
    	-> sid int ,
    	-> sname varchar(20),
    	-> sage int);
- 查看表的结构
		mysql> show create table student;
        mysql> desc student;
- 删除表
		mysql> drop table student;
- 修改表的字段
```java
1.添加表的字段
mysql> alter table student add [column] sgender varchar(2);
2.删除表的字段
mysql> alter table student drop [column]  sgender;
3.修改表的字段类型
mysql> alter table student modify [column] remark varchar(100);
4.修改表的字段名字
mysql> alter table student change sname name varchar(2);
```
- 修改表的名字
		mysql> alter table student rename to teacher;


### 数据的操作
- 增
		INSERT INTO  teacher VALUES(1,'张三',29,"java");
        -- 插入部分字段
		INSERT INTO teacher(sid,NAME) VALUES(2,'李四');
- 删
		-- 删除所有的数据
		DELETE FROM teacher ;
        -- 删除指定
		DELETE FROM teacher WHERE sid=2;
- 改
		--修改数据
		UPDATE teacher SET remark='java';
        -- 带条件的修改
		UPDATE teacher SET sage=22 WHERE sid=2;
        -- 修改多个字段
		UPDATE teacher SET NAME='王五',remark='lua' WHERE sid=2;
- 查
		SELECT * FROM teacher;
        
        
## 数据约束
- 默认值(DEFALUT)
- 非空 (NOT NULL)
- 唯一(UNIQUE)
- 主键(非空+唯一)
- 自增长(AUTO_INCREMENT)
- 外键(CONSTRAINT 外表_主表_fk FOREIGN KEY(外键id) REFERENCES 外表(外键id))
	- 要修改数据的顺序,先修改副表,在修改主表数据
- 级联操作(ON UPDATE CASCADE  ON DELAETE CASCADE)

## 数据库设计
- 每张表每个字段必须是不可分割的独立单元
- 表的每个字段和表的逐渐都依赖
- 每张表的主键之外的其他字段都和主键有直接依赖关系
## 多表查询
- 交叉连接查询
		- select empNmae,deptname from employee,dept;
- 内连接查询(1,确定查询的表,2 确定哪些字段,3,表 域表之间链接条件(连接条件数量=表-1))
		-  select empNmae,deptname from employee,dept where employee.deptId=dept.id;
        - 语法二
        - SELECT empNmae,deptname FROM employee INNER JOIN dept ON employee.deptId=dept.id;
- 左外连接查询,使用左边表的数据去匹配右边表的数据,如果不符合反NULL
		select e.empNmae,d.deptname from employee e left OUTER join  dept d on e.deptId=d.id;
- 右外连接
		- SELECT e.empNmae,d.deptname FROM dept d  RIGHT OUTER JOIN  employee e ON e.deptId=d.id;
- 自连接
		select e1.name,e2,name2 from employee e1 left outer join employee e2  on e1.id=e2.pid;
- 

## 存储过程
- 带上流程控制语句(if, while)
- 服务器端执行,移植性差

```mysql
-- 存储过程
-- 声明结束符
DELIMITER $
CREATE PROCEDURE pro_test()
BEGIN 
	SELECT * FROM employee;
END $ 

-- 执行存储过程
CALL pro_test(参数);
参数:
	in:表示输入参数,
    out:输出参数,从存储过程返回结果
DELIMITER $
CREATE PROCEDURE pro_findByid(IN eid INT)
BEGIN
	SELECT * FROM employee WHERE id =eid;
END $

CALL pro_findByid(1);
-- mysql全局变量 内置变量
-- SHOW VARIABLES;
-- 查看某个全局变量 select @@变量名
-- 会话变量:临时变量 定义 set @变量=值 使用 select @变量
-- 局部变量,只要存储过程执行完毕,局部变量就丢失
-- 调用

call pro_getOut2(@abc);
select @abc;
-- 删除存储过程
drop PROCEDURE pro_getOut;
```

## 触发器
- 当操作了某张表时,希望同时触发一些动作行为,可以使用触发器完成
- 例如当向员工表插入一个信息是,调试向日志表记录
		CREATE TRIGGER empAdd AFTER INSERT ON employee FOR EACH ROWINSERT INTO tesst(content) VALUES('aaa');
## mysql权限问题

- 修改mysql的用户密码
   	- update user set PASSWORD=PASSWROD('12345') where user='root';

- 分配权限
		- GRANT 权限 on 数据库.表 TO '账号'@'账号类型' IDENTIFIED BY '密码'
        - grant select on day16.employee to 'eric'@'localhost|%' IDENTIFIED BY '123456'
        
## 备份
- mysqldump -uroot -p day17>c:/bak.sql
## 恢复
- mysql -uroot -p day17 < d:/back.sql