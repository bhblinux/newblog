---
title: DBUTILs
author: Bhblinux
tags:
  - java
categories: []
date: 2018-05-14 21:21:00
---
## 元数据
- DatabaseMetaData
- ParameterMetaData
- ?generateSimpleParameterMetadata=true
- ResultSetMetaData

## DBUtils 
1. DbUtils
2. QueryRunner 两行代码搞定增删改查
	- QueryRunner() --需要控制事务时,使用这组方法
    - QueryRunner(DataSource ds) --不需要控制事务用这组方法
    
```java
import java.sql.SQLException;
import org.apache.commons.dbutils.DbUtils;
import org.apache.commons.dbutils.QueryRunner;
import org.junit.Test;
import com.mchange.v2.c3p0.ComboPooledDataSource;
public class demo04 {
	@Test
	public void test01() throws SQLException{
		QueryRunner runner=new QueryRunner(new ComboPooledDataSource());
		runner.update("update dept set deptname=? where id =?", "bb",4);
	}
}




            for (User u:query
                 ) {
                System.out.println(u);

            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    @Test
    public void test5() throws SQLException {
        QueryRunner queryRunner = new QueryRunner(new ComboPooledDataSource());
        //返回结果及中的第一行指定列的值，单个查询
        Object query = queryRunner.query("select count(*) from user", new ScalarHandler());
        System.out.println(query);
    }

}

```

3. ResultSetHandler 实现类
- ArrayHandler:把结果集中的第一行数据转成对象数组。
	- 	ArrayListHandler：把结果集中的每一行数据都转成一个对象数组，再存放到List中。
	- !!!!BeanHandler：将结果集中的第一行数据封装到一个对应的JavaBean实例中。
	- 	!!!!BeanListHandler：将结果集中的每一行数据都封装到一个对应的JavaBean实例中，存放到List里。
	- 	MapHandler：将结果集中的第一行数据封装到一个Map里，key是列名，value就是对应的值。
	- 	MapListHandler：将结果集中的每一行数据都封装到一个Map里，然后再存放到List
	- 	ColumnListHandler：将结果集中某一列的数据存放到List中。
	- 	KeyedHandler(name)：将结果集中的每一行数据都封装到一个Map里(List<Map>)，再把这些map再存到一个map里，其key为指定的列。
	- 	!!!!!ScalarHandler:获取结果集中第一行数据指定列的值,常用来进行单值查询
