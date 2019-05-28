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

##### 3、非空和唯一约束