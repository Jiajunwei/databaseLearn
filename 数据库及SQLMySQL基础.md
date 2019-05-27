## 数据库及SQL/MySQL基础

### 八、DDL（数据定义语言）之操作数据库

##### 1.数据库

- 查看所有数据库：show databases;
- 切换（选择要操作的）数据库：use database_name
- 创建数据库：create database [if not exists] database_name [CHARSET=utf8]
- 删除数据库：drop database [if exists] database_name
- 修改数据库编码：alter database database_name character set utf8

### 九、数据类型介绍

##### 2.数据类型（列类型）

- int：整型；
- double：浮点型，例如double(5,2)表示最多5位，其中必须有2位小数，即最大值为999.99
- decimal：浮点型，在表单涉及“金额”方面使用该类型，因为不会出现精度缺失的问题；
- char：固定长度字符串类型，char(255 byte)，不足指定长度，补足到指定长度；
- varchar：可变长度字符串类型，varchar(65535)，不足指定长度，不补足到指定长度，zhangsan；
- text（clob）：字符串类型，mysql独有，tinytext、text、mediumtext、longtext；
- blob：字节类型；
- date：日期类型，yyy-MM-dd；
- time：时间类型，格式为：hh:mm:ss；
- timeStamp：时间戳类型；

### 十一、DDL（数据定义语言）之操作表

##### 3.表的创建、删除、修改、表结构的查看

- 创建表：

  1. CREATE TABLE [IF NOT EXISTS] 表名（

     列名 列类型，

     列名 列类型，

     ......

     列名 列类型

     ）；

- 查看当前数据库中所有表名称：SHOW TABLES;

- 查看表结构：DESC 表名；

- 删除表：DROP TABLE table_name;

- 修改表：前缀：ALTER TABLE 表名

  1. 修改之添加列：

     ALTER TABLE table_name ADD（

     列名 列类型，

     列名 列类型，

     ......

     ）；

  2. 修改之修改列类型（如果被修改的列已存在数据，那么新的类型可能会影响到已存在的数据）：

     ALTER TABLE table_name MODIFY（

     列名 列类型，

     列名 列类型，

     ......

     ）；

  3. 修改之修改列名：

     ALTER TABLE table_name CHANGE（

     原列名 新列名 列类型，

     原列名 新列名 列类型，

     ......

     ）；

  4. 修改之删除列：

     ALTER TABLE 表名 DROP 列名；

  5. 修改之修改表名：

     ALTER TABLE 原表名 RENAME TO  新表名；

### 十二、DML（数据操作语言）

DML（数据库操作语言，它是对表记录的操作（增、删、改））

##### 1.插入数据

- INSERT INTO 表名（列名1，列名2，......） VALUES （列值1，列值2，......）
  1. 在表名后给出要插入的列，其他没有指定的列等同插入Null。所以插入记录总是插入一行，不可能是半行。
  2. 在VALUES后给出列值，值的顺序和个数必须与前面指定的列对应。
- INSERT INTO 表名 VALUES （列值1，列值2）
  1. 没有给出要插入的列，那么表示插入所有列。
  2. 值的个数必须是改表列的个数。
  3. 值的顺序必须与表创建时给出的列的顺序相同。


##### 2.修改数据

- UPDATA 表名 SET 列名1 = 列值1，列名2 = 列值2，...... [WHERE 条件]
- 条件时可选的：
  1. 条件必须是一个boolean类型的值或表达式：UPDATA tb_person SET gender='男'，age = 18 WHERE id = 1;
  2. 运算符：=、!=、<>、>、<、>=、<=、BETWEEN...AND、IN（....）、IS NULL、NOT、OR、AND

##### 3.删除数据

- DELETE FORM 表名 [WHERE 条件]
- TRUNCATE TABLE 表名
  1. TRUNCATE是DDL语句，它是先删除drop该表，再create该表，且无法回滚！！！

### 十五、DCL（数据控制语言）

##### 1.创建用户

- CREATE USER 用户名@IP地址 IDENTIFIED BY '密码'；

  用户只能在指定的IP地址上登录

- CREATE USER 用户名@'%' IDENTIFIED BY '密码'；

  用户可以在任意的IP地址上登录

##### 2.给用户授权

- GRANT 权限1，权限2，......权限n ON 数据库.* TO 用户名@IP地址

  1. 权限、用户、数据库
  2. 给用户分派在指定的数据库上的指定的权限
  3. 例如：GRANT CREATE, ALTER，DROP，INSERT，UPDATE，DELETE，SELECT ON mydb1.* TO  user1@localhost；

- GRANT ALL ON  数据库.* TO 用户名@IP地址

  给用户分派在指定的数据库上的所有权限

##### 3.撤销授权

- GRANT 替换为 REVOKE

##### 4.查看权限

- SHOW GRANTS FOR 用户名@IP地址

##### 5.删除用户

- DROP USER 用户名@IP地址

  

### 十六、DQL（数据查询语言）

##### 1.基础查询之列控制

###### 1）查询所有列

- SELECT * FROM 表名；

  "*"表示查询所有列


###### 2）查询指定列

- SELECT 列1 [，列2，......列n] FROM 表名；

###### 3）完全重复的记录只查一次

当查询的结果中多行记录一模一样时，

- SELECT DISTINCT * FROM 表名；
- SELECT DISTINCT 列1 [，列2，......列n] FROM 表名；

###### 4）列运算

1. 数量类型的列可以做加减乘除运算

   - SELECT sal*1.5 FROM emp；

   - SELECT sal+comm FROM emp；

2. 字符串类型可以做连续运算

   - SELECT CONCAT（'$'，sal）FROM emp; 

3. 转换NULL值

   - 有时需要把NULL转换成其他值，例如com+1000时，如果com列存在NULL值，那么NULL转换为0进行计算
   - SELECT IFNULL（comm，0）+ 1000 FROM emp;
   - IFNULL（comm，0）：如果comm中存在NULL值，那么当成0来运算

4. 给列起别名

   - SELECT IFNULL（comm，0）+ 1000 AS 奖金 FROM emp;
   - AS后面的为别名，AS也可以省略。

##### 2.基础查询之条件查询

###### 1）条件查询（AND、IN、BETWEEN）

- 与前面介绍的UPDATE和DELETE语句一样，SELECT语句也可以使用WHERE字句来控制记录。
  1. SELECT 书名,售价 FROM bookpub WHERE 售价<80 AND 书名 IS NOT NULL;
  2. SELECT 书名,售价 FROM bookpub WHERE 作者 IN ('房大伟等', '苏宇等');
  3. SELECT 书名,售价 FROM bookpub WHERE 售价 BETWEEN 70 AND 80;

###### 2）模糊查询

- 模糊查询使用运算符：LIKE。'_'匹配任意一个字符，注意，只匹配一个字符而不是多个；'%'匹配0~N个任意字符。

  1. SELECT * FROM bookpub WHERE 作者 LIKE '张__';

     上面语句查询的是姓张，名字有三个字组成的作者。

  2. SELECT * FROM bookpub WHERE 作者 LIKE '___';

     上面语句查询的是名字有三个字组成的作者。

  3. SELECT * FROM bookpub WHERE 作者 LIKE '张%';

     上面语句查询的是姓张的所有作者。

  4. SELECT * FROM bookpub WHERE 作者 LIKE '%阿%';

     上面语句查询的是姓名中间有‘阿、’以‘阿’开头或者以‘阿’结尾（即包含‘阿’）的作者，而不是只有中间有‘阿’的作者，因为%匹配0~N个字符。

  5. SELECT * FROM bookpub WHERE 作者 LIKE '%';

     这个条件等同于不存在，可以查询出所有不为NULL的作者。

- A

- A