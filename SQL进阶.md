## SQL进阶

### 一、mysql编码问题

##### 1、查看MySQL数据库编码

1. SHOW VARIABLES LIKE 'char%';	结果如下：

   ```xml
   +--------------------------+-----------------------------------------------------+
   | Variable_name            | Value                                               |
   +--------------------------+-----------------------------------------------------+
   | character_set_client     | utf8                                                |
   | character_set_connection | utf8                                                |
   | character_set_database   | utf8                                                |
   | character_set_filesystem | binary                                              |
   | character_set_results    | utf8                                                |
   | character_set_server     | utf8                                                |
   | character_set_system     | utf8                                                |
   | character_sets_dir       | E:\ProgramFiles\mysql-8.0.11-winx64\share\charsets\ |
   +--------------------------+-----------------------------------------------------+
   ```

   

##### 2、编码解释

1. character_set_client ：MySQL 使用该编码来解读客户端发来的数据，例如该编码为utf8，那么如果客户端发送过来的数据不是utf8，那么就会出现乱码。
2. character_set_results ：MySQL会把数据转换成该编码后再发送给客户端，例如该编码为utf8，如果客户端不适用utf8解读，那么就会出现乱码。
3. 其他编码只要支持中文即可，也就是不能使用latin

##### 3、控制台乱码问题

1. 插入或修改时出现乱码：
   - 这是因为cmd下默认使用GBK，而character_set_client 不是GBK，只需让这两个编码相同即可。
   - 修改cmd的编码不方便，所以设置character_set_client 为GBK即可
2. 查询出的数据为乱码
   - 这是因为cmd下默认使用GBK，而character_set_results不是GBK，只需让这两个编码相同即可。
   - 修改cmd的编码不方便，所以设置character_set_results为GBK即可
3. 设置变量的语句
   - set character_set_client = gbk;
   - set character_set_results = gbk;
   - 注意，设置变量只对当前连接有效，退出窗口重新登录mysql后还需重新设置变量。
   - 也可以在my.ini中设置：default-character-set=gbk

##### 4、指定默认编码

- 我们在安装MySQL时已经指定了默认编码为utf8，所以在创建数据库、创建表时都无需再次指定编码。为了一劳永逸，可以在my.ini中设置：character-set-server=utf8

### 二、mysql备份与恢复数据库

##### 1、数据库导出SQL脚本

- mysqldump -u用户名 -p密码 数据库名>生成的脚本文件路径
- 例如：mysqldump -uroot -p123 mydb1>D:\mydb1.sql
- 注意：结尾没有分号，不要登录mysql，直接在cmd下运行
- 注意：生成的脚本文件中不包含create database语句

##### 2、执行SQL脚本

1. 第一种方式
   - mysql -u用户名 -p密码 数据库<脚本文件路径
   - 例如：先删除mydb1库，再重新创建mydb1库，然后执行mysql -uroot -p123 mydb1<D:\mydb1.sql
   - 注意：结尾没有分号，不要登录mysql，直接在cmd下运行
2. 第二种方式
   - 登录mysql
   - source SQL脚本路径
   - 例如：先删除mydb1库，再重建mydb1库，切换到mydb1库，source d:\mydb1.sql

### 三、约束之主键约束

约束是添加在列上的，用来约束列的！

##### 1、主键约束（唯一标识）及添加、删除

1. ****非空****

2. ****唯一****

3. ****被引用****

4. 当表的某一列被指定为主键后，该列就不能为空，不能有重复值出现。

5. 创建表时指定主键的两种方式

   - CREATE TABLE stu(

     sid        CHAR(6) PRIMARY KEY,

     sname  VARCHAR(20),

     age       INT,

     gender  VARCHAR(10)

     );

   - CREATE TABLE stu(

     sid         CHAR(6) ,

     sname   VARCHAR(20),

     age        INT,

     gender   VARCHAR(10),

     PRIMARY KEY(sid)

     );

   - 上面两个都是指定sid列为主键列，即为sid列添加主键约束。

6. 修改表时指定主键：ALTER TABLE stu ADD PRIMARY KEY(sid);

7. 删除主键：ALTER TABLE stu DROP PRIMARY KEY;

##### 2、主键自增长

1. 因为主键列的特性是：必须唯一、不能为空，所以我们通常会指定主键类为整型，然后设置其自动增长，这样可以保证在插入数据时主键列的唯一和非空特性。

2. 创建表时指定主键自增长

   - CREATE TABLE stu(

     sid        INT PRIMARY KEY AUTO_INCREMENT,

     sname  VARCHAR(20),

     age       INT,

     gender  VARCHAR(10)

     );

3. 修改表时设置主键自增长：

   - ALTER TABLE stu CAHNGE sid sid INT AUTO_INCREMENT;

4. 修改表时删除主键自增长：

   - ALTER TABLE stu CAHNGE sid sid INT ;

5. 测试主键自增长：

   - INSERT INTO stu VALUES （NULL，'zhangsan'，23，'male'）;
   - INSERT INTO stu （sname,age,gender）（'zhangsan'，23，'male'）;
   - 执行完上述两个语句后，sid会自动增加

##### 3、非空约束

- 因为某些列不能设置为NULL值，所以可以对列添加非空约束

- 例如：CREATE TABLE stu（

  ​						sid 	     INT PRIMARY KEY AUTO_INCREMENT,

  ​						sname	VARCHAR(20) NOT NULL,

  ​						age             INT,

  ​						gender        VARCHAR(10)

  ​						） ;

- 对sname列设置了非空约束

##### 4、唯一约束

- 因为某些列不能设置重复的值，所以可以对列添加唯一约束

- 例如：CREATE TABLE stu（

  ​						sid 	     INT PRIMARY KEY AUTO_INCREMENT,

  ​						sname	VARCHAR(20) NOT NULL UNIQUE,

  ​						age             INT,

  ​						gender        VARCHAR(10)

  ​						） ;

- 对sname列设置了非空约束、唯一约束

### 四、概述模型、对象模型、关系模型

1. 当我们要完成一个软件系统时，需要把系统中的实体抽取出来，形成概念模型。

2. 例如部门、员工都是系统中的实体。概念模型中的实体最终会成为Java中的类（对象模型）、数据库中的表（关系模型）。

   - is a：继承
   - has a：成员变量，1对1、1对多、多对多
   - use a：成员函数的参数，用参数做事

3. 实体之间还存在着关系，关系有三种：

   - 1对1：一个人对应一张身份证，一张身份证对应一个人

   - 1对多：一个员工从属一个部门，一个部门有很多员工，员工是多方，部门是一方。

   - 多对多：一个学生可以选多门课程，一个课程可以被多个学生选。
   - 主从关系：少的是主，多的是从

4. 概念模型在Java中成为实体类（javaBean）

5. 类就使用成员变量来完成关系，一般都是双向关联

6. 多对一双向关联，即员工关联部门，部门也关联员工

   ```java
   //一对多
   class Employee {			//多方关联一方
       ...
       private Department department;
   }
   class Department {			//一方关联多方
      ...
      private List<Employee> employees;
   }
   //一对一
   class Husband {
       ...
       private Wife wife;
   }
   class Wife {
       ...
       private Husband husband;
   }
   //多对多
   class Student {
       ...
       private List<Teacher> teachers;
   }
   class Teacher {
       ...
       private List<Student> students;
   }
   ```

   

### 五、外键约束

1. 外键必须是另一表（也可以是本身）的主键的值（外键要引用主键）

2. 外键可以重复

3. 外键可以为空

4. 一张表中可以有多个外键

5. 多方引用一方

6. 对象模型：可以双向关联，而且引用的是对象

7. 关系模型：多方引用一方，而且引用的只是主键，而不是一整行记录

8. 实例：

   ```mysql
   --创建主表
   CREATE TABLE [IF NOT EXISTS] dept ( 
       deptno INT PRIMARY KEY AUTO_INCREMENT,
       deptname VARCHAR ( 50 ) 
   );
   --插入数据
   INSERT INTO dept [( deptno, deptname )] VALUES ( 10,'研发部'  );
   INSERT INTO dept [( deptno, deptname )] VALUES ( 20,'人力部'  );
   INSERT INTO dept [( deptno, deptname )] VALUES ( 30,'财务部'  );
   INSERT INTO dept [( deptno, deptname )] VALUES ( 40, '销售部' );
   
   --创建分表，添加外键约束
   CREATE TABLE [IF NOT EXISTS] emp (
   		empno INT PRIMARY KEY AUTO_INCREMENT,
   		ename VARCHAR ( 50 ),
   		dno INT,
   	CONSTRAINT fk_emp_dept FOREIGN KEY ( dno ) REFERENCES dept ( deptno ) 
   	);
   --插入数据
   INSERT INTO emp (empno, ename) values (null, 'zhangsan') 	--没有插入dno，即外键为空
   INSERT INTO emp (empno, ename, dno) values (null, 'lisi', 10) 
   INSERT INTO emp (empno, ename, dno) values (null, 'wangwu', 10) --插入的dno与上一条一样，即外键可以重复
   INSERT INTO emp (empno, ename, dno) values (null, 'wangwu', 80)--80在dept表的deptno列中不存在，所以会报错
   
   --修改添加外键约束
   --假设emp没有外键约束
   ALTER TABLE emp ADD CONSTRAINT fk_emp_dept FOREIGN KEY ( dno ) REFERENCES dept ( deptno );
   
   ```

   


### 六、一对一关系

在表中建立一对一关系比较特殊，需要让其中一张表的主键，既是主键又是外键。

```mysql
CREATE TABLE husband(
hid int PRIMARY KEY AUTO_INCREMENT,
...
);
CREATE TABLE wife(
wid int PRIMARY KEY AUTO_INCREMENT,
...
ADD CONSTRAINT fk_wife_wid FOREIGN KEY(wid) REFERENCES husband(hid)
)
```

其中wife表的wid既是主键，又是相对husband表的外键！husband.hid是主键，不能重复！wife.wid是主键不能重复，又是外键，必须来自husband.hid。所以如果在wife表中有一条记录的wid为1，那么wife表中的其他记录的wid就不能再是1了，同时在husband.hid中必须存在1这个值。

### 七、多对多关系

在表中建立多对多关系需要使用中间表，即需要三张表，在中间表使用两个外键，分别引用其他两个表的主键。

```mysql
CREATE TABLE student(
sid INT PRIMARY KEY AUTO_INCREMENT,
...
);
CREATE TABLE teacher(
tid INT PRIMARY KEY AUTO_INCREMENT,
...
);
CREATE TABLE stu_tea(
sid INT,
tid INT,      
...
ADD CONSTRAINT fk_stu_tea_sid FOREIGN KEY(sid) REFERENCES student(sid),
ADD CONSTRAINT fk_stu_tea_tid FOREIGN KEY(tid) REFERENCES teacher(tid),    
);
```

这时在stu_tea这个中间表中的每条记录都是来说明sutdent和teacher表的关系

例如在stu_tea表中的记录，sid为1001，tid为2001，这说明编号为1001的学生有一个编号为2001的老师