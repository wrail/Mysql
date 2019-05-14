# Mysql知识简单整理和回顾

## 存储引擎相关

### 几个常见的存储引擎

> 下边就简单对常见的三大存储引擎进行简单的优缺点分析和使用场合分析

InnoDB

支持事务，支持外键，对崩溃修复和并发有很好的支持，对内存使用较高，支持表级锁。适用于对事务完整要求比较高，并且需要并发控制。当然，频繁的更新，删除等操作也可以选择InnoDB。相比于其他几个常用的引擎，插入速度较慢。

MyISAM

相比于InnoDB，它不支持事务操作，不支持外键，支持行锁，出入数据快（数据插入速度快），对空间和内存使用较少，对于并发和完整性要求较低就可以选择MyISAM。

MEMORY

MEMORY所有数据都在内存里（对内存使用高），那速度是想当然的快，支持表锁，安全性不高。如果需要一些插入速度很快并且需要的安全性不是很高，并且大小不是很大时就可以选择MEMORY

### 几个查看存储引擎的命令

查看所有的存储引擎：show engines

查看默认的引擎：show variables like "%storage_engine"

### 修改默认存储引擎

修改Mysql下的my.ini或my.cnf，在mysql组下添加”default-storage-engine=INNODB“，然后重启。

SQL：set default_storage_engine=MyISAM

## 数据表

### 常见的数据库对象

| ` 常见的数据库对象` |              |                                                              |
| ------------------- | ------------ | ------------------------------------------------------------ |
| `对象名称`          | `对应关键字` | `描述`                                                       |
| `表`                | table        | 表是存储数据的逻辑单元，以行和列的形式存在，列就是字段，行就是记录 |
| `数据字典 `         |              | 就是系统表，存放数据库相关信息的表。系统表的数据通常由数据库系统维护，程序员通常不应该修改，只可查看 |
| `约束`              | constraint   | 执行数据校验的规则，用于保证数据完整性的规则                 |
| `视图`              | view         | 一个或者多个数据表里的数据的逻辑显示，视图并不存储数据       |
| `索引`              | index        | 用于提高查询性能，相当于书的目录                             |
| `函数`              | function     | 用于完成一次特定的计算，具有一个返回值                       |
| `存储过程`          | procedure    | 用于完成一次完整的业务处理，没有返回值，但可通过传出参数将多个值传给调用环境 |
| `触发器 `           | trigger      | 相当于一个事件监听器，当数据库发生特定事件后，触发器被出发，完成相应的处理 |

### 范式

![1557303636898](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557303636898.png)

第一范式：关系模式R下所有的属性都是不可再分的数据项。

第二范式：如果关系模式满足一范式，并且每一个非主属性都**完全依赖**于R的键。

第三范式：在二范式基础上消除传递依赖。

### 三范式的设计过程

以SLC（学生，位置，课程）为例，下边先列出会用到的属性释义。

- Sno：学生学号
- Sname：学生姓名
- Sdept：所在系
- Local：系地址
- Cno：课程编号
- Grade：成绩

1. 第一范式

   列出所有属性，意思相近就进行合并以免出现数据冗余

   SLC（Sno，Sname，Sdept，Local，Cno，Grade）

2. 第二范式

   对属性进行分析，让非主属性完全依赖于主属性（可能会包含传递依赖）

   SLC中的主属性：Sno，Cno

   > 1.先从Sno进行分析

   - Sno--->Sname    
   - Sno--->Sdept  
   - Sno--->Local

   可以看到所有的非主属性都完全依赖于主属性（仅仅主属性就可以确定非主属性的值，就是完全依赖于主属性），并且可以明显看到Sdept--->Local也是可以的，但是传递依赖并不影响第二范式要求。

   > 2.再从Cno分析

   发现没有单独可以使用Cno而确定的非主属性

   > 3.最后从（Sno，Cno）进行分析

   (Sno,Cno)--->Grade

   > 发现最终的结果都是由非主属性完全依赖于主属性，因此这就满足的第二范式的要求

3. 第三范式

   对第二范式中未消除的传递依赖进行拆解，为什么要对传递依赖进行拆解呢？因为在一个表中存在多个复杂的依赖关系，会因此引起一些异常。并且也存在数据的冗余。

   可以发现在第二范式设计中存在有传递依赖Sno--->Sdept--->Local，因此就对这两个依赖进行分表，分为各自的完全依赖。

   最终的设计效果：

   S：(Sno,Sname,Sdept)

   L：(Sdept,Local)

   SCG：(Sno,Cno,Grade)

   经过三范式的修订，各个属性独立，很大程度消除了数据冗余和一些异常（更新，插入，删除），在一般数据库设计都设计为第三范式，简单有效。

   在第三范式后还有BCNF（对第三范式的修订，可以完全消除冗余，消除了异常，实现彻底的分离），第四范式，第五范式。

### 表之间的关系

一对一：比如学生表（student）对应学生成绩表（g_student），学生成绩表外键是学生号（stuId），学生表中的Id和学生成绩表中的stuId是一一对应的。

一对多：一个班级表（class）对应多个学生表（student），一个班级包含多个学生。

多对多：比如学生选课，课程有多个，学生也有多个，学生得选多个课程，每个课程得被多个学生选，这就是多对多的关系。

## 数据表的sql操作

### 数据表的创建和删除

1. 创建表

```Sql
create table tablename(
   属性名 数据类型 [约束]
   ...
   ...

);
```

2. 查看表的定义信息

```Sql
describe tablename;
```

3. 查看表的详细定义信息

```Sql
show create table tablename;
```

4. 删除表

```Sql
drop table tablename;
```

### 操作表的约束

| 约束           | 说明                                        |
| :------------- | ------------------------------------------- |
| primary key    | 主键，可作为表的唯一标识                    |
| foreign key    | 外键，联系其他表的主键                      |
| not null       | 此属性不能为空值                            |
| auto_increment | 自增                                        |
| unique         | 唯一                                        |
| default        | 设置默认值默认（pname ptype default '123'） |

#### 设置表的主键约束

1. 单字段主键

```Sql
create table tablename (

    propName PropType primary key
    ...
);
```

2. 多字段主键

```Sql
create table tablename (

    PropName1 PropType1,
    PropName2 PropType2,
    ...
    [constraint pk_name] primary key (PropName1,PropName2);
);
```

#### 设置表单外键约束

> 外键约束可以保证多个表之间的参照完整性

```Sql
create table tablename_1(

    PropName1_1 PropType1_1;
    PropName1_2 PropType1_2;
    #PropName2_1是父表要被此表参照的属性，字表的属性类型一定要和父表类型一致！
    constraint fk_prop foreign key (PropName_1) references tablename_2 (PropName2-1);
);
```

注意点

- [ ] 父表和字表的属性类型和大小要和子表相同
- [ ] 更新时必须先更新父表再更新子表，否则会报错

操作演示：

```Sql
mysql> create table class (
    -> classno int(10) primary key not null,
    -> classname varchar(10),
    -> stunum int );
Query OK, 0 rows affected (0.16 sec)

mysql> create table student (
    -> stuno int primary key,
    -> stucalssno int(10),
    -> stuname varchar(10) not null,
    -> constraint fk_classno foreign key (stucalssno) references class(classno));
Query OK, 0 rows affected (0.07 sec)

mysql> describe class
    -> ;
+-----------+-------------+------+-----+---------+-------+
| Field     | Type        | Null | Key | Default | Extra |
+-----------+-------------+------+-----+---------+-------+
| classno   | int(10)     | NO   | PRI | NULL    |       |
| classname | varchar(10) | YES  |     | NULL    |       |
| stunum    | int(11)     | YES  |     | NULL    |       |
+-----------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> describe student;
+------------+-------------+------+-----+---------+-------+
| Field      | Type        | Null | Key | Default | Extra |
+------------+-------------+------+-----+---------+-------+
| stuno      | int(11)     | NO   | PRI | NULL    |       |
| stucalssno | int(10)     | YES  | MUL | NULL    |       |
| stuname    | varchar(10) | NO   |     | NULL    |       |
+------------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```

### 修改表

1. 修改表名

```Sql
alter table tablename rename [to] anothername;
```

2. 增加字段

```Sql
#在表末尾加一个字段
alter table tablename add propname proptype;
#在表头增加一个字段
alter table tablename add propname proptype first;
#在某个字段后加一个字段
alter table tablename add propname proptype after prepropname;
```

3. 删除字段

```Sql
alter table tablename drop propname;
```

4. 修改字段

```Sql
#修改属性类型
alter table tablename modify propname proptype;
#修改字段名称
alter table tablename change oldname newname oldtype;
#同时修改名称和类型
alter table tablename change oldname newname newtype;
#修改字段顺序
alter table tablename modify pname1 protype first/after pname2;
```

操作演示：

```Sql
mysql> use sqltest;
Database changed
mysql> create table user(
    -> id int not null auto_increment primary key,
    -> username varchar(10) not null);
Query OK, 0 rows affected (0.15 sec)

mysql> describe user;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id       | int(11)     | NO   | PRI | NULL    | auto_increment |
| username | varchar(10) | NO   |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
2 rows in set (0.00 sec)

mysql> show create table user;
+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                                                                                                             |
+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| user  | CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(10) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci |
+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> alter table user rename to user1;
Query OK, 0 rows affected (0.14 sec)

mysql> show tables;
+-------------------+
| Tables_in_sqltest |
+-------------------+
| user1             |
+-------------------+
1 row in set (0.00 sec)

mysql> describe user1;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id       | int(11)     | NO   | PRI | NULL    | auto_increment |
| username | varchar(10) | NO   |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
2 rows in set (0.00 sec)

mysql> alter table user1 modify id int(10);
Query OK, 0 rows affected (0.14 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(10)     | NO   | PRI | NULL    |       |
| username | varchar(10) | NO   |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
2 rows in set (0.01 sec)

mysql> alter table user1 add pet varchar(10);
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(10)     | NO   | PRI | NULL    |       |
| username | varchar(10) | NO   |     | NULL    |       |
| pet      | varchar(10) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
3 rows in set (0.01 sec)

mysql> alter table user1 add petfirst varchar(10) first;
Query OK, 0 rows affected (0.10 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| petfirst | varchar(10) | YES  |     | NULL    |       |
| id       | int(10)     | NO   | PRI | NULL    |       |
| username | varchar(10) | NO   |     | NULL    |       |
| pet      | varchar(10) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> alter table user1 add petsecond varchar(10) after petfirst;
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+-----------+-------------+------+-----+---------+-------+
| Field     | Type        | Null | Key | Default | Extra |
+-----------+-------------+------+-----+---------+-------+
| petfirst  | varchar(10) | YES  |     | NULL    |       |
| petsecond | varchar(10) | YES  |     | NULL    |       |
| id        | int(10)     | NO   | PRI | NULL    |       |
| username  | varchar(10) | NO   |     | NULL    |       |
| pet       | varchar(10) | YES  |     | NULL    |       |
+-----------+-------------+------+-----+---------+-------+
5 rows in set (0.00 sec)

mysql> alter table user1 drop petsecond;
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| petfirst | varchar(10) | YES  |     | NULL    |       |
| id       | int(10)     | NO   | PRI | NULL    |       |
| username | varchar(10) | NO   |     | NULL    |       |
| pet      | varchar(10) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> alter table user1 modify petfirst varchar(11);
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> alter table user1 change petfirst petchanged varchar(10);
Query OK, 0 rows affected (0.13 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+------------+-------------+------+-----+---------+-------+
| Field      | Type        | Null | Key | Default | Extra |
+------------+-------------+------+-----+---------+-------+
| petchanged | varchar(10) | YES  |     | NULL    |       |
| id         | int(10)     | NO   | PRI | NULL    |       |
| username   | varchar(10) | NO   |     | NULL    |       |
| pet        | varchar(10) | YES  |     | NULL    |       |
+------------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> alter table user1 change petchanged newpet int(11);
Query OK, 0 rows affected (0.10 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| newpet   | int(11)     | YES  |     | NULL    |       |
| id       | int(10)     | NO   | PRI | NULL    |       |
| username | varchar(10) | NO   |     | NULL    |       |
| pet      | varchar(10) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> alter table user1 modify newpet int(11) after id;
Query OK, 0 rows affected (0.11 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(10)     | NO   | PRI | NULL    |       |
| newpet   | int(11)     | YES  |     | NULL    |       |
| username | varchar(10) | NO   |     | NULL    |       |
| pet      | varchar(10) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> alter table user1 modify newpet int(11) first;
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| newpet   | int(11)     | YES  |     | NULL    |       |
| id       | int(10)     | NO   | PRI | NULL    |       |
| username | varchar(10) | NO   |     | NULL    |       |
| pet      | varchar(10) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> alter table user1 modify newpet int(10) after id;
Query OK, 0 rows affected (0.15 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user1;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| id       | int(10)     | NO   | PRI | NULL    |       |
| newpet   | int(10)     | YES  |     | NULL    |       |
| username | varchar(10) | NO   |     | NULL    |       |
| pet      | varchar(10) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```

> 问题和总结

1. 自增字段为什么不能设置默认值？

​     可以很轻松的想明白，如果自增字段有默认值就不能算是自增了，自增字段可以是任何的整数类型（不插入设置的话是从1开始自增），如果插入一个比当前自增值大的，下一次会在这个基础加1.

2. 设置外键必须先要有父表，才能设置外键。
3. 怎么删除一个已经被其它表设置为外键的父表，可以先删除子表再删除父表，也可以先把子表的外键约束去掉，然后再删除父表。

## Mysql数据操作

### 插入数据

1. 插入一条完整的数据（如果属性顺序一致可省略）

```Sql
insert into tablename[(filed1...)] values(filed1Value...);
```

2. 插入不完整数据

```Sql
insert into tablename (filed1...) values(filedValue...);
```

3. 插入多条数据

```Sql
insert into tablename (filed1...)
values(filed1Name1...),(file1Name2...).....;
```

4. 插入多条不完整数据

```Sql
insert into tablename (filed1...)
values(filed1Name1...),(file1Name2...).....;

```

5. 插入查询结果

```Sql
insert into tableName1 (filed1...) 
select (filed1...) from tableName2
where ....;

```

### 更新数据

1. 更新特定数据记录

```Sql
update tableName set filed1 = value1, filed2 = value2 ... wherhe ...;

```

2. 更新所有数据记录

就是改变where条件为全部（只要所有属性满足的条件都可以）

### 删除数据

```Sql
delete from tableName where ...

```

## Mysql中的数据类型

### 整数类型

| 整数类型  | 字节数 |
| --------- | ------ |
| tinyint   | 1      |
| smallint  | 2      |
| mediumint | 3      |
| int       | 4      |
| bigint    | 8      |

### 浮点数类型

| 类型                         | 字节数 |
| ---------------------------- | ------ |
| float                        | 4      |
| double                       | 8      |
| decimal（M，D）或dec（M，D） | M+2    |

### 日期类型

| 类型      | 字节数 | 零值                   |
| --------- | ------ | ---------------------- |
| year      | 1      | 0000                   |
| date      | 4      | 0000：00：00           |
| time      | 3      | 00：00：00             |
| datetime  | 8      | 0000-00-00 00：00：00  |
| timestamp | 4      | 00000000000000（14位） |

### 字符串类型

1. char和varchar类型

```Sql
char(m)/varchar(m)

```

都是开始指定了最长的大小，char是固定的，varchar是可变的，varchar占用的是字符串长度+1的空间。

> 值得注意的是，varchar（5）中装‘abcde’占用的字节数是6，这是可允许范围。
>
> 存的如果是‘abc ’（最后带有空格），它占用的是5个字节。

2. text类型

| 类型       |
| ---------- |
| tinytext   |
| text       |
| mediumtext |
| longtext   |

3. emun类型

```Sql
filedName enum ('value1','value2'...);

```

4. set类型

```Sql
filedName set ('value1','value2'...);

```

set中末尾的空格会被删除（如果末尾有空格的话）

### 二进制类型

| 类型        | 取值范围                                |
| ----------- | --------------------------------------- |
| binary（M） | 字节数位M，长度位0~M的定长二进制字符串  |
| varbinary   | 变长的，字节数为值长度+1                |
| bit（M）    | M位二进制，最大64                       |
| tinyblob    | 可变长二进制数据，最多255字节           |
| blob        | 可变长二进制数据，最多2的16次方-1个字节 |
| midiumblob  | 最多2的24次方-1个字节                   |
| longblob    | 最多2的32次方-1个字节                   |

> BLOB类型一般用来保存比较大的二进制数据

------

> 既然有这么多的数据类型该怎么选取合适的数据类型？

1. 如果需要小数就用浮点类型（按照大小和精度里来选取），整数就用整数类型（按大小来取）
2. 对于时间日期类，如果只需要年份就选year，如果需要年月日就选date，如果只记录时间就选time，要记日期和时间的话就可以选择datetime或者是timestamp。datetime的精度比timestamp大，如果需要时间范围大的话就选datetime，如果是根据时区来显示就选择timestamp。
3. enum和set，enum最多可以有65565个成员，set最多64个，enum适合比如性别字段（‘男‘或’女‘），set适合比如爱好（会有多个爱好）。
4. text和blob，text是存文本，blob是存二进制。

> 问题及其解答

1. Mysql存储路径应该怎么存

可以用char，varchar，text等存，值得注意的是在路径中使用"\ "会被过滤掉，解决方法是写成“ \ \”或者“/”来代替。

2. Mysql中怎么使用布尔类型

可以通过BOOL或者Boolean来定义，但是它们最终的类型转换为tinyint(1)，也就是说布尔等价于tinyint（1）。

## Mysql运算符

### 算数运算符

| 符号 | 表达式形式    | 作用               |
| ---- | ------------- | ------------------ |
| +    | x1+x2...      | add                |
| -    | x1-x2...      |                    |
| *    | x1*x2...      |                    |
| /    | x1/x2         |                    |
| DIV  | x1 DIV x2     | 除法，如上，返回商 |
| %    | x1%x2         | 求余               |
| MOD  | MOD（x1，x2） | 求余               |

1. 操作数

```Sql
mysql> select 1+2+3 加法,3-3-8 减法,9/3 除法,8*9 乘法,8 div 4 除法,11%5 求模余数,13 mod 5 求模s余数;

+------+------+--------+------+------+----------+-----------+
| 加法 | 减法 | 除法   | 乘法 | 除法 | 求模余数 | 求模s余数 |
+------+------+--------+------+------+----------+-----------+
|    6 |   -8 | 3.0000 |   72 |    2 |        1 |         3 |
+------+------+--------+------+------+----------+-----------+
1 row in set (0.01 sec)

```

2. 除了可以直接操作数，也可以操作表中的字段

```Sql
 select var1,var2,var3,var1+var2 add,var1-var2 decr,var1 mod var3 from math;

```

### 比较运算符

![img](https://images2015.cnblogs.com/blog/990532/201611/990532-20161104195340565-861208871.jpg)

判断相等还有<=>,和=的区别是它可以操作空值，而=不行。<>和！=都不能操作空值。

```Sql
mysql> select 1<=>1 equal;
+-------+
| equal |
+-------+
|     1 |
+-------+
1 row in set (0.01 sec)

mysql> select 1<=>2 equal;
+-------+
| equal |
+-------+
|     0 |
+-------+
1 row in set (0.00 sec)
mysql> select 'a' in ('a','b') ;
+------------------+
| 'a' in ('a','b') |
+------------------+
|                1 |
+------------------+
1 row in set (0.01 sec)

mysql> select 1 between 3 and 3;
+-------------------+
| 1 between 3 and 3 |
+-------------------+
|                 0 |
+-------------------+
1 row in set (0.00 sec)

```

#### regexp运算符：正则匹配

正则表达式通过模式匹配一些列字符串.

Mysql支持的模式字符

| 模式字符         | 含义                             |
| ---------------- | -------------------------------- |
| ^                | 匹配字符串的开始标识             |
| $                | 匹配字符串的结束标识             |
| .                | 匹配字符串中任何一个字符         |
| [字符集合]       | 匹配字符集合中的任意一个字符     |
| [^字符集合]      | 匹配集合外的任意一个字符         |
| str1\|str2\|str3 | 匹配这几个字符串的其中一个字符串 |
| *                | 匹配字符0个或多个                |
| +                | 匹配一个字符                     |
| 字符串{N}        | 字符串出现n次                    |
| 字符串（M，N）   | 字符串至少出现m次，最多n次       |

```Sql
1. 字符 ‘^' 查询以特定字符或字符串开头的记录

SELECT * FROM user WHERE email REGEXP '^a'
字符 ‘^' 匹配以特定字符或字符串开头的记录，以上语句查询邮箱以 a 开头的记录

2. 字符 ' 查询以特定字符或字符串结尾的记录

SELECT * FROM user WHERE phone REGEXP '0$'
字符 ‘$' 匹配以特定字符或字符串结尾的记录，以上语句查询邮箱以 0 结尾的记录

3. 用符号“.”；来代替字符串中的任意一个字符

SELECT * FROM user WHERE email REGEXP 'a.c'
查询邮箱 a、c 之间有一个字符的记录，'.' 相当于是一个占位符。如果写成 REGEXP ‘a..c' , 即a、c之间有两个点，则表示邮箱中 a、c 之间要有两个字符。

4.使用“*”匹配多个字符


SELECT * FROM user WHERE email REGEXP 'm*'
查询所有邮箱中有 m 的记录。

SELECT * FROM user WHERE email REGEXP '^am*'
查询邮箱字母 a开头， a后面有字母 m 的记录。其中'*' 表示0次或以上。

5. 用字符“+”表示紧跟的字符

SELECT * FROM user WHERE email REGEXP 'm+'
查询所有邮箱中有 m 的记录。

SELECT * FROM user WHERE email REGEXP '^am+'
查询邮箱字母 a开头， a后面紧跟字母 m 的记录。其中'+' 表示紧跟字符。

6. “|” 分隔条件匹配指定字符串

SELECT * FROM user WHERE email REGEXP 'qq.com|163.com'
正则表达式可以匹配指定的字符串，字符串之间使用 “|” 分隔。

7. “[]” 表示集合匹配指定字符串中的任意一个

SELECT * FROM user WHERE email REGEXP '[az]'
”[]“ 指定一个集合，以上表示查询邮箱中带有 a或z或两者都有的邮箱。也可以用来匹配数字集合，比如 [0-9] 表示集合区间所有数字，[a-z] 表示集合区间所有字母。

8. “[^]”匹配指定字符以外的字符

SELECT * FROM user WHERE email REGEXP '[^a-d1-3]'
如上匹配邮箱中不包含 a、b、c、d 且 不包含 1、2、3 的记录。

9. 使用{n,} 或 {n,m} 来指定字符串连接出现的次数

SELECT * FROM user WHERE email REGEXP 'b{2}'
表示字母 b 至少出现 2 次。

SELECT * FROM user WHERE email REGEXP 'ba{1,3}'
表示字符串 ba 至少出现1次，至多出现3次。

```

```Sql
mysql> select 'liming' regexp '^li';
+-----------------------+
| 'liming' regexp '^li' |
+-----------------------+
|                     1 |
+-----------------------+
1 row in set (0.00 sec)

mysql> select 'liming' regexp '^li','wangwu' regexp 'l';
+-----------------------+---------------------+
| 'liming' regexp '^li' | 'wangwu' regexp 'l' |
+-----------------------+---------------------+
|                     1 |                   0 |
+-----------------------+---------------------+
1 row in set (0.00 sec)

mysql> select 'liming' regexp 'g$','wangwu' regexp 'l$';
+----------------------+----------------------+
| 'liming' regexp 'g$' | 'wangwu' regexp 'l$' |
+----------------------+----------------------+
|                    1 |                    0 |
+----------------------+----------------------+
1 row in set (0.00 sec)

mysql> select 'liming' regexp '.....g';
+--------------------------+
| 'liming' regexp '.....g' |
+--------------------------+
|                        1 |
+--------------------------+
1 row in set (0.00 sec)

mysql> select 'a' regexp '[a,b]';
+--------------------+
| 'a' regexp '[a,b]' |
+--------------------+
|                  1 |
+--------------------+
1 row in set (0.00 sec)

mysql> select 'a' regexp '[a,b]','a' regexp '[^a]';
+--------------------+-------------------+
| 'a' regexp '[a,b]' | 'a' regexp '[^a]' |
+--------------------+-------------------+
|                  1 |                 0 |
+--------------------+-------------------+
1 row in set (0.00 sec)

mysql> select 'abc' regexp 'acc|abc|cba';
+----------------------------+
| 'abc' regexp 'acc|abc|cba' |
+----------------------------+
|                          1 |
+----------------------------+
1 row in set (0.00 sec)

mysql> select 'liming' regexp 'c*g','liming' regexp 'l+g','liming' regexp 'c+g','liming'regexp 'n+g';
+-----------------------+-----------------------+-----------------------+----------------------+
| 'liming' regexp 'c*g' | 'liming' regexp 'l+g' | 'liming' regexp 'c+g' | 'liming'regexp 'n+g' |
+-----------------------+-----------------------+-----------------------+----------------------+
|                     1 |                     0 |                     0 |                    1 |
+-----------------------+-----------------------+-----------------------+----------------------+
1 row in set (0.00 sec)

```

### 逻辑与算符

![1557497676202](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557497676202.png)

### 位运算符

![1557497644595](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557497644595.png)

### 运算优先级

![1557497608279](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557497608279.png)

## 单表查询

#### 查询语法

```Sql
select [all|distinct] xxx from tableName/viewName,.. 
[where xxx] [group by <列名1> [having xxx]]
[order by <列名2> [ASC|DESC]]

```

单表查询结合四则运算

```Sql
mysql> select var1+var2+var3 sum from math;
+------+
| sum  |
+------+
|   13 |
+------+
1 row in set (0.00 sec)

mysql> select var1*var2 mo from math;
+------+
| mo   |
+------+
|    6 |
+------+
1 row in set (0.00 sec)


```

模糊查询中_代表一个字符，%代表若干个。非空值is not null，between and，and，or，！等等

#### 统计函数

- count（）函数：统计表中记录的条数
- AVG（）函数：统计字段的平均值
- SUM（）函数：统计字段的总和
- MAX（）函数：查询字段的最大值
- MIN（）函数：查询字段的最小值

语法：

```Sql
select 函数（field）from tableName where condition

```

**count（*）和count（field）的区别**

- *统计的是所有记录（包含空记录）
- field统计的是指定字段的记录统计，忽略空值

#### 分组查询

分组就是把某个/某些field按照一定的规律排列（默认是升序）分为很多组（每一项为一组）。

用法：

```Sql
select field1 from xxx where condition group by field1[filed2...] [having field1>10]; 

```

例子：

```Sql
# 将各个年龄分组，并且显示各个年龄段的平均薪水（因为后边进行了分组，因此前边的聚集函数都是在一个组的基础上进行）
select age,AVG(salary) average from teacher group by age;

```

#### LIMIT限制记录

语法

```Sql
select xxx 
from tableName
where condition limit offset_start,row_count;

```

offset_start：起始偏移量，不写就是0

row_count：显示的行数

#### 使用正则表达式查询

```Sql
1. 字符 ‘^' 查询以特定字符或字符串开头的记录

SELECT * FROM user WHERE email REGEXP '^a'
字符 ‘^' 匹配以特定字符或字符串开头的记录，以上语句查询邮箱以 a 开头的记录

2. 字符 ' 查询以特定字符或字符串结尾的记录

SELECT * FROM user WHERE phone REGEXP '0$'
字符 ‘$' 匹配以特定字符或字符串结尾的记录，以上语句查询邮箱以 0 结尾的记录

3. 用符号“.”；来代替字符串中的任意一个字符

SELECT * FROM user WHERE email REGEXP 'a.c'
查询邮箱 a、c 之间有一个字符的记录，'.' 相当于是一个占位符。如果写成 REGEXP ‘a..c' , 即a、c之间有两个点，则表示邮箱中 a、c 之间要有两个字符。

4.使用“*”匹配多个字符


SELECT * FROM user WHERE email REGEXP 'm*'
查询所有邮箱中有 m 的记录。

SELECT * FROM user WHERE email REGEXP '^am*'
查询邮箱字母 a开头， a后面有字母 m 的记录。其中'*' 表示0次或以上。

5. 用字符“+”表示紧跟的字符

SELECT * FROM user WHERE email REGEXP 'm+'
查询所有邮箱中有 m 的记录。

SELECT * FROM user WHERE email REGEXP '^am+'
查询邮箱字母 a开头， a后面紧跟字母 m 的记录。其中'+' 表示紧跟字符。

6. “|” 分隔条件匹配指定字符串

SELECT * FROM user WHERE email REGEXP 'qq.com|163.com'
正则表达式可以匹配指定的字符串，字符串之间使用 “|” 分隔。

7. “[]” 表示集合匹配指定字符串中的任意一个

SELECT * FROM user WHERE email REGEXP '[az]'
”[]“ 指定一个集合，以上表示查询邮箱中带有 a或z或两者都有的邮箱。也可以用来匹配数字集合，比如 [0-9] 表示集合区间所有数字，[a-z] 表示集合区间所有字母。

8. “[^]”匹配指定字符以外的字符

SELECT * FROM user WHERE email REGEXP '[^a-d1-3]'
如上匹配邮箱中不包含 a、b、c、d 且 不包含 1、2、3 的记录。

9. 使用{n,} 或 {n,m} 来指定字符串连接出现的次数

SELECT * FROM user WHERE email REGEXP 'b{2}'
表示字母 b 至少出现 2 次。

SELECT * FROM user WHERE email REGEXP 'ba{1,3}'
表示字符串 ba 至少出现1次，至多出现3次。

```

**问题与解答**

1. Mysql中的通配符和正则匹配的区别

通配符使用的范围狭窄，只能在like中用，正则表达式可以实现多种多样的匹配，需要regexp配合使用。

2. DISTINCT可以作用于所有列吗？

distinct 是作用于整合查询列表的，因此有一点区别都会被显示出来（也就是组合值不同都会被显示）。

3. 为什么有的时候，使用正确的通配符表达式却没有出结果？

有可能是因为结尾存在空格，使用Trim函数把空格去掉。

## 多表查询

1. 并操作（UNION）

把具有相同字段数目和字段类型的表合并到一起。

2. 笛卡儿积

笛卡儿积是一种关系的代数表达，假设集合A={a, b}，集合B={0, 1, 2}，则两个集合的[笛卡尔](https://www.baidu.com/s?wd=笛卡尔&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)积为{(a, 0), (a, 1), (a, 2), (b, 0), (b, 1), (b, 2)}。在数据库中一张表就是一个集合。

#### 内连接查询

![img](https://img-blog.csdn.net/20181005173658980?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqdDk4MDQ1MjQ4Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

是全连接的改进，可以在on上条件筛选两个表重叠的部分（阴影部分），全连接就是直接进行连接没有条件筛选（on）会查出很多没有用的数据。

##### 自链接

就是与自身表进行连接，如果以前有10条记录，连接后就会有100条记录。

```Sql
mysql> select s1.*,s2.*
    -> from stuff s1 inner join stuff s2
    -> on s1.id = s2.id;

```

##### 等值链接

就是在on上通过（=）来实现等值条件

```Sql
mysql> select s1.*,s2.*
    -> from stuff s1 inner join stuff s2
    -> on s1.id=s2.id;
    
+----+--------+------+--------+--------+----+--------+------+--------+--------+
| id | dep_id | name | gender | salary | id | dep_id | name | gender | salary |
+----+--------+------+--------+--------+----+--------+------+--------+--------+
|  1 |      1 | 小王 | 男     |   1000 |  1 |      1 | 小王 | 男     |   1000 |
|  2 |      1 | 小李 | 男     |   1500 |  2 |      1 | 小李 | 男     |   1500 |
|  3 |      2 | 小陈 | 女     |   2000 |  3 |      2 | 小陈 | 女     |   2000 |
|  4 |      3 | 小良 | 女     |   2000 |  4 |      3 | 小良 | 女     |   2000 |
|  5 |      4 | 小张 | 女     |   3500 |  5 |      4 | 小张 | 女     |   3500 |
|  6 |      3 | 小星 | 男     |   1500 |  6 |      3 | 小星 | 男     |   1500 |
|  7 |      1 | 小彭 | 男     |   3400 |  7 |      1 | 小彭 | 男     |   3400 |
|  8 |      2 | 小龙 | 男     |   6000 |  8 |      2 | 小龙 | 男     |   6000 |
|  9 |      4 | 小玉 | 女     |   7600 |  9 |      4 | 小玉 | 女     |   7600 |
| 10 |      3 | 小陈 | 男     |   4555 | 10 |      3 | 小陈 | 男     |   4555 |
+----+--------+------+--------+--------+----+--------+------+--------+--------+
10 rows in set (0.00 sec)

```

##### 不等值链接

就是on中的条件不是等于，其余都可以

```Sql
mysql> select s1.*,s2.*
    -> from stuff s1 inner join stuff s2
    -> on s1.salary>s2.salary;
#执行过程就是先从s1的第一个开始和s2的全部根据salary进行比较，满足的留下来，不满足的去掉。
+----+--------+------+--------+--------+----+--------+------+--------+--------+
| id | dep_id | name | gender | salary | id | dep_id | name | gender | salary |
+----+--------+------+--------+--------+----+--------+------+--------+--------+
|  2 |      1 | 小李 | 男     |   1500 |  1 |      1 | 小王 | 男     |   1000 |
|  3 |      2 | 小陈 | 女     |   2000 |  1 |      1 | 小王 | 男     |   1000 |
|  4 |      3 | 小良 | 女     |   2000 |  1 |      1 | 小王 | 男     |   1000 |
|  5 |      4 | 小张 | 女     |   3500 |  1 |      1 | 小王 | 男     |   1000 |
|  6 |      3 | 小星 | 男     |   1500 |  1 |      1 | 小王 | 男     |   1000 |
|  7 |      1 | 小彭 | 男     |   3400 |  1 |      1 | 小王 | 男     |   1000 |
|  8 |      2 | 小龙 | 男     |   6000 |  1 |      1 | 小王 | 男     |   1000 |
|  9 |      4 | 小玉 | 女     |   7600 |  1 |      1 | 小王 | 男     |   1000 |
| 10 |      3 | 小陈 | 男     |   4555 |  1 |      1 | 小王 | 男     |   1000 |


```

#### 外连接查询

这是开发中常用的一种连接方式，左外连接时，左表是主表，右表是从表，通过ON条件连接后，主表的所有记录都会被查询出来，而从表只会显示符合ON条件的记录，查不到的数据以null填充。

![img](https://img-blog.csdn.net/20181005211357263?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqdDk4MDQ1MjQ4Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

以左边表为参照表

```Sql
mysql> SELECT * FROM stuff s left outer JOIN department d ON s.`dep_id` = d.`id`;
+----+--------+------+--------+--------+------+--------+
| id | dep_id | name | gender | salary | id   | name   |
+----+--------+------+--------+--------+------+--------+
|  1 |      1 | 小王 | 男     |   1000 |    1 | 前台部 |
|  2 |      1 | 小李 | 男     |   1500 |    1 | 前台部 |
|  3 |      2 | 小陈 | 女     |   2000 |    2 | 后台部 |
|  4 |      3 | 小良 | 女     |   2000 |    3 | 算法部 |
|  5 |      4 | 小张 | 女     |   3500 |    4 | 运维部 |
|  6 |      3 | 小星 | 男     |   1500 |    3 | 算法部 |
|  7 |      1 | 小彭 | 男     |   3400 |    1 | 前台部 |
|  8 |      2 | 小龙 | 男     |   6000 |    2 | 后台部 |
|  9 |      4 | 小玉 | 女     |   7600 |    4 | 运维部 |
| 10 |      3 | 小陈 | 男     |   4555 |    3 | 算法部 |
+----+--------+------+--------+--------+------+--------+
10 rows in set (0.00 sec)

```



##### 右外连接

![img](https://img-blog.csdn.net/20181005213457811?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqdDk4MDQ1MjQ4Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

以右边表为参照表

```Sql
mysql> SELECT * FROM stuff s right outer JOIN department d ON s.`dep_id` = d.`id`;
+------+--------+------+--------+--------+----+--------+
| id   | dep_id | name | gender | salary | id | name   |
+------+--------+------+--------+--------+----+--------+
|    1 |      1 | 小王 | 男     |   1000 |  1 | 前台部 |
|    2 |      1 | 小李 | 男     |   1500 |  1 | 前台部 |
|    7 |      1 | 小彭 | 男     |   3400 |  1 | 前台部 |
|    3 |      2 | 小陈 | 女     |   2000 |  2 | 后台部 |
|    8 |      2 | 小龙 | 男     |   6000 |  2 | 后台部 |
|    4 |      3 | 小良 | 女     |   2000 |  3 | 算法部 |
|    6 |      3 | 小星 | 男     |   1500 |  3 | 算法部 |
|   10 |      3 | 小陈 | 男     |   4555 |  3 | 算法部 |
|    5 |      4 | 小张 | 女     |   3500 |  4 | 运维部 |
|    9 |      4 | 小玉 | 女     |   7600 |  4 | 运维部 |
| NULL |   NULL | NULL | NULL   |   NULL |  5 | 运营部 |
+------+--------+------+--------+--------+----+--------+
11 rows in set (0.00 sec)


```



#### 复合条件连接查询

复合条件查询是在连接查询的基础上加了更多的限制，使得查询结果更精确。

例如：查询所有成绩总分超过450的学生信息（学号，姓名等）和成绩总分和班级信息（班级号，班级名，班主任）和老师信息还有成绩总和

student表，class表，sc表

```Sql
select s.*,c.*,sc.chinese+sc.english+sc.math sum 
from student s inner join class c on s.classid=c.id
inner join sc on s.id=sc.sid and sc.chinese+sc.english+sc.math>450;

```

#### 合并查询数据记录

Mysql中通过Union关键字来实现并操作，可以通过将多个select的查询结果合并在一起组成新的关系。

Union

```Sql
SELECT column,... FROM table1 
UNION 
SELECT column,... FROM table2

```

Union All

```Sql
SELECT column,... FROM table1 
UNION  ALL
SELECT column,... FROM table2

```

Union和Union All 的区别是，Union会取消重复元素，UnionAll不会取消重复。能够合并的前提是元素属性列相同

比如两个表名不相同，但是属性列相同就可以合并。或者属性列有相同的就可以把这些相同的属性列的表合并。

#### 子查询

> 为什么使用子查询？因为如果表过大的话，直接连接会对两个表进行笛卡儿积操作，数据大的话有可能会造成死机，因此，在处理比较大的数据量的时候，先使用count（*）先看看这两个表的数据的笛卡儿积是否在Mysql的接收范围之内，然后再进行选择，子查询就可以解决这一问题。

##### **带有比较的运算符的子查询**

> 可以是=,>,<,!=,>=,<=,<>(和！=是等价的)等

比如查询薪资水平为高级的所有员工的信息。（1，2，3为低级，4为高级，5为特级）

```Sql
select * from employ
where salary >= (select salary from level = 4) 
and salary <= (select salary from level = 5);

```

##### 带有关键字IN的子查询

查询employee中的deptid，在dept表中有的deptid（也就是employee中的deptid包含在dept表中的deptid中的员工）

```Sql
select * from employee 
where deptid in (select deptid form dept);

```

IN /NOT IN

##### 带有EXISTS的子查询

查询dept中是否存在deptid为4的部门，如果存在，再查询employee的记录

```Sql
select * from employee where exists (select deptid from dept where deptid = 4);

```

##### 带有关键字Any的子查询

查询出Emp表中的员工比任意一个销售员(SALESMAN)工资低的员工姓名、工作、工资。

```Sql
SELECT ENAME,JOB,SAL FROM EMP
WHERE SAL<ANY (SELECT SAL FROM EMP WHERE JOB='SALESMAN') 

```

##### 带有All的子查询

和Any用法相同

![1557499544669](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557499544669.png)

## 索引

### 索引的简介

索引由数据表的一列或者多列组成，其作用是提高对表中数据的查询速度（创建在表上）。通过索引，查询数据时不必把所有的信息都读完，只是查询索引列，如果查询的是非索引，那就逐条信息进行匹配。

#### 索引的优点

1. 通过创建唯一索引，可以保证数据库每一行数据的唯一性
2. 可以大大提高查询速度
3. 可以加速表与表的连接
4. 可以显著的减少查询中分组和排序的时间。

#### **索引的缺点**

1. 创建索引和维护索引需要时间，而且数据量越大时间越长
2. 创建索引需要占据磁盘的空间，如果有大量的索引，可能比数据文件更快达到最大文件尺寸
3. 当对表中的数据进行增加，修改，删除的时候，索引也要同时进行维护，降低了数据的维护速度

> 因此如果想要在有索引的表中插入记录，插入记录时，数据库会按照索引进行排序，降低了插入的速度，因此可以先删除索引，然后再插入，然后再添加索引。

#### 索引的分类

1. 普通索引

用表中的普通列构建的索引，没有任何限制，它的值是否唯一或非空由列本身属性来决定

2. 唯一索引

使用UNIQUE可以设置索引为唯一索引

3. 全文索引

使用参数FULLTEXT可以设置索引为全文索引。仅适用于MyISAM，全文索引只能创建在char，varchar，text类型字段上，查询数据量较大的字符串类型的字段时，使用它可以提高查找速度。

4. 单列索引

在表中的单个字段上创建索引，只根据该字段进行索引。

5. 多列索引

在表的多个字段上创建一个索引，该索引指向创建时的多个字段，但是**只有在查询条件中使用了第一个字段时才会被使用**。

6. 空间索引

使用参数SPATIAL可以设置索引为空间索引。只能建立在空间数据类型上，可以提高系统获取空间数据的效率。Mysql中的空间类型有GEOMETRY（几何），POINT（点），LINESTRING（一系列的点和连接这些点的线段），PLOYGON（多边形）。但是目前只有MyIASM支持空间检索，并且索引字段不能为空。

#### 索引的设置原则

1. 选择唯一性索引

唯一才能保证速度

2. 为经常需要排序，分组和联合操作的字段建立索引

经常进行order by ，group by，distinct，union等操作的字段，为其排序很浪费时间，因此建议设置索引

3. 为常作为查询条件的字段建立索引

如果经常以某个字段作为查询条件，那很有必要为该字段建立索引，可以提高整体的查找效率

4. 限制索引数目

索引都要占用磁盘空间，索引过多导致磁盘空间需要越大，也会对后来的索引重构造成麻烦

5. 尽量使用数据量小的索引

如果索引的值很长，查询速度也会受到影响，比如char（5）和cahr（100）的检索速度就不同，后者肯定比前者慢

6. 尽量使用值前缀来索引

如果需要索引的类型值很长，比如是text类型，那就可以对其前面的一部分进行索引，可以提高索引速度

7. 删除不再使用或使用很少的索引

### 索引的SQL操作

#### 索引的创建和查看

1. 普通索引的创建

   **创建表的时候创建索引**

   ```Sql
   #在创建表的时候创建,index和key的作用是一样的，length是可选，指定索引长度，所以必须是字符串才能使用
   create table tableName (
   
       propName1 type [constraint],
       ...
       propNamen type [constraint]
       
       [unqiue|fulltext|spatail] index|key
       [indexname](propName1 [(length)] [ASC|DESC])
   );
   
   #例如
   create table class01 (
       classno int(5),
       cname varchar(10),
       location varchar(20),#逗号不能忘
       INDEX index_classno(classno)
   );
   
   mysql> explain select * from class01 where classno = 1;
   
   | id | select_type | table   | partitions | type | possible_keys | key           | key_len | ref   | rows | filtered | Extra |
   +----+-------------+---------+------------+------+---------------+---------------+---------+-------+------+----------+-------+
   |  1 | SIMPLE      | class01 | NULL       | ref  | index_classno | index_classno | 5       | const |    1 |   100.00 | NULL  |
   +----+-------------+---------+------------+------+---------------+---------------
   #可以使用explain来查看当前指向的sql语句有没有索引被使用
   #可以使用show table create className [\G];查看详细信息。
   mysql> show create table class01 \G;
   *************************** 1. row ***************************
          Table: class01
   Create Table: CREATE TABLE `class01` (
     `classno` int(5) DEFAULT NULL,
     `cname` varchar(10) DEFAULT NULL,
     `location` varchar(20) DEFAULT NULL,
     KEY `index_classno` (`classno`)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
   1 row in set (0.00 sec)
   
   
   ```

   - UNIQUE：可选。表示索引为唯一性索引。
   - FULLTEXT；可选。表示索引为全文索引。
   - SPATIAL：可选。表示索引为空间索引。

- INDEX和KEY：用于指定字段为索引，两者选择其中之一就可以了，作用是一样的。

  - 索引名：可选。给创建的索引取一个新名称。
  - 字段名1：指定索引对应的字段的名称，该字段必须是前面定义好的字段。
  - 长度：可选。指索引的长度，必须是字符串类型才可以使用。
  - ASC：可选。表示升序排列。
  - DESC：可选。表示降序排列。

  **在已存在的表上创建索引**

  ```Sql
  create [UNIQUE|FULLTEXT|SPATAIL] index indexName 
  on tableName (propname[length] [ASC|DESC]); 
  
  #给class的name创建一个唯一索引
  mysql> create unique index index_name on class(classname);
  Query OK, 0 rows affected (0.09 sec)
  Records: 0  Duplicates: 0  Warnings: 0
  
  mysql> show create table class;
  
  | Table | Create Table   
  
  | class | CREATE TABLE `class` (
    `classno` int(10) NOT NULL,
    `classname` varchar(10) DEFAULT NULL,
    `stunum` int(11) DEFAULT NULL,
    PRIMARY KEY (`classno`),
    UNIQUE KEY `index_name` (`classname`),
    KEY `index_classno` (`classno`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci |
  
  1 row in set (0.00 sec)
  
  ```

  

  **修改表添加索引**

  ```sQL
  #修改表创建
  alter table tableName add index|key indexname (propname [(length)] [ASC|DESC]);
  
  #给class表classno添加索引
  mysql> alter table class add index index_classno(classno);
  Query OK, 0 rows affected (0.20 sec)
  Records: 0  Duplicates: 0  Warnings: 0
  
  mysql> show create table class;
  
  | Table | Create Table                                                                                                                                             
  | class | CREATE TABLE `class` (
    `classno` int(10) NOT NULL,
    `classname` varchar(10) DEFAULT NULL,
    `stunum` int(11) DEFAULT NULL,
    PRIMARY KEY (`classno`),
    KEY `index_classno` (`classno`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci |
  
  1 row in set (0.01 sec)
  
  ```

2. 唯一索引的创建

   唯一索引创建和普通索引一样就是前面加了UNIQUE关键字

   **在创建表的时候创建**

   ```Sql
   create table tableName(
   
       propName1 type1 [constraint],
       ....
       propNamen typen [constraint],
       
       UNIQUE index|key [indexname](prop[length][ASC|DESC])
   );
   
   ```

   **在已经创建的表上**

   ```SQl
   create unique index indexName on tableName(propName[length][ASC|DESC]); 
   
   ```

   **修改表添加索引**

   ```SQl
   alter table tableName add unique index indexName(propName [length][ASC|DESC]);
   
   ```

3. 全文索引

   和前面的一样，只是换了一个关键词为FULLTEXT。

4. 多列索引

   多列索引就是在创建索引时，所关联的字段不是一个字段而是多个。

   **创建表时自动创建**

   ```Sql
   create table tableNa me(
   
       propName1 type [constraint],
       ...
       propName type [constraint],
       
       index [indexname](propName1 [length] [ASC|DESC],propName2 [length] [ASC|DESC])
   );
   
   ```

   **在已存在的表上创建**

   ```Sql
   create index indexName 
   on tableName (propName1 [length] [ASC|DESC],
                ...
                );
   
   ```

   **通过修改表创建**

   ```Sql
   alter table tableName add index indexName (propName1 [length] [ASC|DESC],....);
   
   ```

#### 索引的删除

```Sql
drop index indexName on tableName;

#删除在上边给class的classname上添加的索引
mysql> drop index index_classno on class;
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show create table class \G;
*************************** 1. row ***************************
       Table: class
Create Table: CREATE TABLE `class` (
  `classno` int(10) NOT NULL,
  `classname` varchar(10) DEFAULT NULL,
  `stunum` int(11) DEFAULT NULL,
  PRIMARY KEY (`classno`),
  UNIQUE KEY `index_name` (`classname`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)


```

**问题与总结**

为什么建议使用短索引？

短索引会减少索引长度可以增加索引效率和减少磁盘空间的占用，因为比如是varchar（255）可能前十个或者前二十个字符就可以确定这段文字的内容，因此建议使用短索引。

## 视图

### 视图的基本概念

#### 什么是视图

MySQL视图是一个虚拟表，其内容由查询定义。通俗的讲，视图就是一条SELECT语句执行后返回的结果集。所以我们在创建视图的时候，主要的工作就落在创建这条SQL查询语句上。

#### 视图的特点

- 视图的列可以来自不同的表，是对表的抽象和逻辑意义上建立新的关系
- 视图是由基本表（实表）产生的表（虚表）
- 视图的建立和删除不影响基本表
- 对视图内容的更新（增，删，改）会影响基本表
- 如果视图来自于多个基本表，那就不允许进行添加和删除

#### 视图的优点

- 视图可以简化逻辑复杂的数据查询，方便操作，特别是查询操作，减少复杂的SQL语句，增强可读性。
- 更加安全，数据库授权命令不能限定到特定行和特定列，但是通过合理创建视图，可以把权限限定到行列级别。

#### 视图的用途

- 权限控制的时候，不希望用户访问表中某些含敏感信息的列，比如salary,身份证号等等。
- 关键信息来源于多个复杂关联表，可以创建视图提取我们需要的信息，简化操作。

视图并不在数据库以存储数据的形式存在，行和列都是来自于自定义视图的查询所引用的基本表，并且在具体引用时，视图是自动生成的。

### 视图的Sql语句

#### 视图的创建

```Sql
create[OR repalace] [ALGORITHM = [UNDEFINED|MERGE|TEMPLATE]]
view viewName[columnlist]
As select statement
[with [CASCADED|LOCAL]CHECK OPTIOn]

```

- 创建（create）语句和替换（replace）语句语法相同。
- ALGORITHM表示视图要使用的算法。UNDEFINED是自动选择算法，MERGE表示将使用的视图语句与视图定义结合起来，使得视图定义的某一部分取代语句对于的部分，TEMPLATE表示将视图的结果存到临时表，然后用临时表来执行SQL语句。
- CASCADED和LOCAL为可选参数，CASCADED为默认值，表示更新视图时要满足所有相关视图和表的条件。LOCAL表示更新视图时满足视图本身条件即可。

视图属于数据库，默认创建当前数据库的视图，如果需要创建其他数据库视图，就必须加上指定的数据库名dbname.viewname。

> 创建视图时需要create view 和 查询select权限。
>
> mysql> select Select_priv,Create_view_priv from mysql.user
>  -> where user='root';
> +-------------+------------------+
> | Select_priv | Create_view_priv |
> +-------------+------------------+
> | Y           | Y                |
> +-------------+------------------+

1. 在单表上创建视图，限制salary在视图的权限

   ```Sql
   mysql> select * from stuff;
   +----+--------+------+--------+--------+
   | id | dep_id | name | gender | salary |
   +----+--------+------+--------+--------+
   |  1 |      1 | 小王 | 男     |   1000 |
   |  2 |      1 | 小李 | 男     |   1500 |
   |  3 |      2 | 小陈 | 女     |   2000 |
   |  4 |      3 | 小良 | 女     |   2000 |
   |  5 |      4 | 小张 | 女     |   3500 |
   |  6 |      3 | 小星 | 男     |   1500 |
   |  7 |      1 | 小彭 | 男     |   3400 |
   |  8 |      2 | 小龙 | 男     |   6000 |
   |  9 |      4 | 小玉 | 女     |   7600 |
   | 10 |      3 | 小陈 | 男     |   4555 |
   +----+--------+------+--------+--------+
   10 rows in set (0.01 sec)
   
   mysql> create view view_stuff
       -> as select id,dep_id,name,gender from stuff ;
   Query OK, 0 rows affected (0.06 sec)
   
   mysql> select * from view_stuff;
   +----+--------+------+--------+
   | id | dep_id | name | gender |
   +----+--------+------+--------+
   |  1 |      1 | 小王 | 男     |
   |  2 |      1 | 小李 | 男     |
   |  3 |      2 | 小陈 | 女     |
   |  4 |      3 | 小良 | 女     |
   |  5 |      4 | 小张 | 女     |
   |  6 |      3 | 小星 | 男     |
   |  7 |      1 | 小彭 | 男     |
   |  8 |      2 | 小龙 | 男     |
   |  9 |      4 | 小玉 | 女     |
   | 10 |      3 | 小陈 | 男     |
   +----+--------+------+--------+
   10 rows in set (0.01 sec)
   
   ```

2. 在多表上创建视图(MERGE算法)

   ```Sql
   mysql> select * from department;
   +----+--------+
   | id | name   |
   +----+--------+
   |  1 | 前台部 |
   |  2 | 后台部 |
   |  3 | 算法部 |
   |  4 | 运维部 |
   |  5 | 运营部 |
   +----+--------+
   5 rows in set (0.00 sec)
   
   mysql> create algorithm=merge View view_stuff_department(id,name,gender,dep_name)
       -> as select s.id,s.name,s.gender,dep.name from stuff s,department dep where s.dep_id =dep.id;
   Query OK, 0 rows affected (0.07 sec)
   
   mysql> select * from view_stuff_department;
   +----+------+--------+----------+
   | id | name | gender | dep_name |
   +----+------+--------+----------+
   |  1 | 小王 | 男     | 前台部   |
   |  2 | 小李 | 男     | 前台部   |
   |  7 | 小彭 | 男     | 前台部   |
   |  3 | 小陈 | 女     | 后台部   |
   |  8 | 小龙 | 男     | 后台部   |
   |  4 | 小良 | 女     | 算法部   |
   |  6 | 小星 | 男     | 算法部   |
   | 10 | 小陈 | 男     | 算法部   |
   |  5 | 小张 | 女     | 运维部   |
   |  9 | 小玉 | 女     | 运维部   |
   +----+------+--------+----------+
   
   ```

#### 查看视图

- DESCRIBE | DESC viewName

- SHOW TABLE STATUS [FROM dbname] [LIKE 'pattern'] (必须是数据库名而不是数据表名)

  显示的是当前数据库的状态，也就是列出所有表的状态和视图的状态详细信息。

  > show table status from sqltest \G;
  >
  > 
  >
  > mysql> show table status from sqltest \G;
  >
  > *************************** 3. row ***************************
  >         Name: department                #表或视图的名称
  >       Engine: InnoDB                        # 使用的引擎名
  >      Version: 10                                  #表的.frm文件的版本号
  >   Row_format: Dynamic              #表的行存储格式 
  >         Rows: 5                                     #表中行的数目（也就是记录的条数）
  > Avg_row_length: 3276                 #表中行的平均长度
  >  Data_length: 16384                    #表数据文件长度
  > Max_data_length: 0                      #表数据文件的最大长度
  > Index_length: 0                            #索引文件的长度
  >    Data_free: 0                               #未使用的字节数
  > Auto_increment: NULL                #自增
  >  Create_time: 2019-05-10 15:30:57        #创建时间
  >  Update_time: 2019-05-10 15:31:23       #最后一次修改时间
  >   Check_time: NULL                   #最后一次检查时间
  >    Collation: utf8_general_ci   #表的字符集
  >     Checksum: NULL                     #表的活性校验
  > Create_options:                             #表的额外选项
  >      Comment:                                 #表的注解
  > *************************** 7. row ***************************
  >         Name: stuff
  >       Engine: InnoDB
  >      Version: 10
  >   Row_format: Dynamic
  >         Rows: 10
  > Avg_row_length: 1638
  >  Data_length: 16384
  > Max_data_length: 0
  > Index_length: 16384
  >    Data_free: 0
  > Auto_increment: NULL
  >  Create_time: 2019-05-10 15:31:41
  >  Update_time: 2019-05-10 15:32:10
  >   Check_time: NULL
  >    Collation: utf8_general_ci
  >     Checksum: NULL
  > Create_options:
  >      Comment:
  > *************************** 9. row ***************************
  >         Name: view_stuff
  >       Engine: NULL
  >      Version: NULL
  >   Row_format: NULL
  >         Rows: 0
  > Avg_row_length: 0
  >  Data_length: 0
  > Max_data_length: 0
  > Index_length: 0
  >    Data_free: 0
  > Auto_increment: NULL
  >  Create_time: 2019-05-11 11:55:22
  >  Update_time: NULL
  >   Check_time: NULL
  >    Collation: NULL
  >     Checksum: NULL
  > Create_options: NULL
  >      Comment: VIEW
  > *************************** 10. row ***************************
  >         Name: view_stuff_department
  >       Engine: NULL
  >      Version: NULL
  >   Row_format: NULL
  >         Rows: 0
  > Avg_row_length: 0
  >  Data_length: 0
  > Max_data_length: 0
  > Index_length: 0
  >    Data_free: 0
  > Auto_increment: NULL
  >  Create_time: 2019-05-11 12:07:56
  >  Update_time: NULL
  >   Check_time: NULL
  >    Collation: NULL
  >     Checksum: NULL
  > Create_options: NULL
  >      Comment: VIEW
  > 10 rows in set (0.04 sec)
  >
  > ERROR:
  > No query specified	

- SHOW CREATE VIEW viewname

- SELECT * FROM information_schema.views where table_name='viewname' \G

#### 修改视图

1. 使用create|repalce语句来修改视图，此语句是如果没有就创建，由就修改

```Sql
create[or replace] [algorithm={undefined|merge|template}]
view viewname[(columnlist)]
as select statement 
[with[CASCADED|LOCAL]CHECK OPTION ]

```

2. 使用ALTER语句修改视图

```Sql
alter [algorithm={undefined|merge|template}]
view viewname[(clonmnlist)] 
as select statement
[with [CASCADED|LOCAL]]

```

![1557550519876](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557550519876.png)

### 更新视图

#### 更新

更新和删除操作和表操作相同。

```Sql
mysql> select * from view_stuff_department;
+----+------+--------+----------+
| id | name | gender | dep_name |
+----+------+--------+----------+
|  1 | 小王 | 男     | 前台部   |
|  2 | 小李 | 男     | 前台部   |
|  7 | 小彭 | 男     | 前台部   |
|  3 | 小陈 | 女     | 后台部   |
|  8 | 小龙 | 男     | 后台部   |
|  4 | 小良 | 女     | 算法部   |
|  6 | 小星 | 男     | 算法部   |
| 10 | 小陈 | 男     | 算法部   |
|  5 | 小张 | 女     | 运维部   |
|  9 | 小玉 | 女     | 运维部   |
+----+------+--------+----------+

mysql> update view_stuff set name = 'hahaha' where id=1;
Query OK, 1 row affected (0.07 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from view_stuff;
+----+--------+--------+--------+
| id | dep_id | name   | gender |
+----+--------+--------+--------+
|  1 |      1 | hahaha | 男     |
|  2 |      1 | 小李   | 男     |
|  3 |      2 | 小陈   | 女     |
|  4 |      3 | 小良   | 女     |
|  5 |      4 | 小张   | 女     |
|  6 |      3 | 小星   | 男     |
|  7 |      1 | 小彭   | 男     |
|  8 |      2 | 小龙   | 男     |
|  9 |      4 | 小玉   | 女     |
| 10 |      3 | 小陈   | 男     |
+----+--------+--------+--------+
10 rows in set (0.00 sec)

mysql> select * from stuff;
+----+--------+--------+--------+--------+
| id | dep_id | name   | gender | salary |
+----+--------+--------+--------+--------+
|  1 |      1 | hahaha | 男     |   1000 |
|  2 |      1 | 小李   | 男     |   1500 |
|  3 |      2 | 小陈   | 女     |   2000 |
|  4 |      3 | 小良   | 女     |   2000 |
|  5 |      4 | 小张   | 女     |   3500 |
|  6 |      3 | 小星   | 男     |   1500 |
|  7 |      1 | 小彭   | 男     |   3400 |
|  8 |      2 | 小龙   | 男     |   6000 |
|  9 |      4 | 小玉   | 女     |   7600 |
| 10 |      3 | 小陈   | 男     |   4555 |
+----+--------+--------+--------+--------+
10 rows in set (0.00 sec)


```

从视图删除10号

```Sql
mysql> select * from stuff;
+----+--------+--------+--------+--------+
| id | dep_id | name   | gender | salary |
+----+--------+--------+--------+--------+
|  1 |      1 | hahaha | 男     |   1000 |
|  2 |      1 | 小李   | 男     |   1500 |
|  3 |      2 | 小陈   | 女     |   2000 |
|  4 |      3 | 小良   | 女     |   2000 |
|  5 |      4 | 小张   | 女     |   3500 |
|  6 |      3 | 小星   | 男     |   1500 |
|  7 |      1 | 小彭   | 男     |   3400 |
|  8 |      2 | 小龙   | 男     |   6000 |
|  9 |      4 | 小玉   | 女     |   7600 |
| 10 |      3 | 小陈   | 男     |   4555 |
+----+--------+--------+--------+--------+
10 rows in set (0.00 sec)

mysql> delete from view_stuff where id = 10;
Query OK, 1 row affected (0.01 sec)

mysql> select * from stuff;
+----+--------+--------+--------+--------+
| id | dep_id | name   | gender | salary |
+----+--------+--------+--------+--------+
|  1 |      1 | hahaha | 男     |   1000 |
|  2 |      1 | 小李   | 男     |   1500 |
|  3 |      2 | 小陈   | 女     |   2000 |
|  4 |      3 | 小良   | 女     |   2000 |
|  5 |      4 | 小张   | 女     |   3500 |
|  6 |      3 | 小星   | 男     |   1500 |
|  7 |      1 | 小彭   | 男     |   3400 |
|  8 |      2 | 小龙   | 男     |   6000 |
|  9 |      4 | 小玉   | 女     |   7600 |
+----+--------+--------+--------+--------+
9 rows in set (0.00 sec)

```

#### 不能更新视图

1. 视图中包含SUM（），COUNT（），MAX（）和MIN（）等聚集函数。
2. 视图包含UNION，UNION ALL，DISTINCT,GROUP BY ,HAVING等关键字。
3. 常量视图（create view view_cons as select 'const' as name;   就是select 出的不是一个列属性而是一个常量值 ）
4. 视图中的select包含子查询
5. 由不可更新视图导出的视图
6. 创建视图时，ALGORITHM为TEMPLATE类型时
7. 视图中没有实体表中的属性列不能更新
8. WITH[CASCADED|LOCAL]CHECK OPTION 也可以决定视图的更新，LOCAL要求只满足视图自身要求即可，CASCADED则要求既满足自身，又要满足相关实体表的要求。

> 一般情况都把视图作为虚拟表，虽然可以更新，但是限制条件很多，没有考虑全面的话会导致更新失败。

### 删除视图

```Sql
drop view viewname [,viewname]

mysql> drop view view_stuff;
Query OK, 0 rows affected (0.12 sec)

mysql> show create view view_stuff;
ERROR 1146 (42S02): Table 'sqltest.view_stuff' doesn't exist

```

## 存储过程和函数

#### 了解存储过程和函数

存储过程和和函数是在数据库中定义一些SQL的集合，一次执行多个SQL来实现某个预期功能，需要的时候调用即可。就和JAVA中的方法，C语言中的函数是一个道理。

并且存储过程和函数是在Mysql服务器中存储和执行的，可以减少客户端和服务端的数据传输。

#### 存储过程和函数的区别

1. 一般来说，存储过程实现的功能要复杂一点，而函数的实现的功能针对性比较强。
2. 存储过程，功能强大，可以执行包括修改表等一系列数据库操作；用户定义函数不能用于执行一组修改全局数据库状态的操作。
3. 对于存储过程来说可以返回参数，如记录集，而函数只能返回值或者表对象。函数只能返回一个变量；而存储过程可以返回多个。存储过程的参数可以有IN,OUT,INOUT三种类型，而函数只能有IN类~~存储过程声明时不需要返回类型，而函数声明时需要描述返回类型，且函数体中必须包含一个有效的RETURN语句。
4. 存储过程，可以使用非确定函数，不允许在用户定义函数主体中内置非确定函数。
5. 存储过程一般是作为一个独立的部分来执行（ EXECUTE 语句执行），而函数可以作为查询语句的一个部分来调用（SELECT调用），由于函数可以返回一个表对象，因此它可以在查询语句中位于FROM关键字的后面。 SQL语句中不可用存储过程，而可以使用函数。

#### 创建存储过程与函数

##### 创建存储过程

语法

```Sql
create procedure procedure_name([proc_param[,...]])
[characteristic...] routine_body

```

![1557587451883](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557587451883.png)

- proc_param：存储过程的参数

  语法

  ```SQL
  [in|out|inout] param_name type
  
  ```

  - in：输入类型
  - out：输出类型
  - inout：输入/输出类型
  - param_name：参数名
  - type：参数类型

- characteristic：存储过程的特性

  - language sql ：说明routine_body是由SQL语句组成
  - [not] deterministic：表示存储过程的执行结果是[不]确定的。确定就是相同的输入相同的输出，默认是not deterministic![1557587667310](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557587667310.png)
  - {contains sql | nosql | reads sql data | modifies sql data}：指明子程序使用sql的限制。分别是包含sql。没有sql，读数据的sql，写数据的sql。系统指定为contains sql。
  - sql security{definer | invoker}：指明谁有权限来执行。definer表示只有定义者才可以，invoker表示拥有权限的调用者可以执行。系统默认为definer。
  - comment'string'：注释信息。

- routine_body：存储过程的SQL代码，可以用begin........end来标志开始和结束

> 存储过程不能重名，推荐写为procedure_xxx,proc_xxxx
>
> 存储过程创建时，默认是contains sql 如果存储过程没有使用sql建议使用no sql

![1557573847890](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557573847890.png)

```SQL
mysql> delimiter ;
mysql> delimiter **
mysql> create procedure proc_salary()
    -> begin
    -> select salary from stuff;
    -> end **
Query OK, 0 rows affected (0.13 sec)

mysql> delimiter ;
mysql> show procedure status \G;

mysql> call proc_salary;
+--------+
| salary |
+--------+
|   1000 |
|   1500 |
|   2000 |
|   2000 |
|   3500 |
|   1500 |
|   3400 |
|   6000 |
|   7600 |
+--------+
9 rows in set (0.01 sec)

Query OK, 0 rows affected (0.02 sec)

```

##### 创建存储函数

```Sql
create function fun_name(fun_param[,...])
[characteristic...] routine_body

```

参数和存储过程相同fun_param格式 param_name type

```Sql
mysql> create function fun_salary(id int(11))
    -> returns int(11)
    -> begin
    -> return(select salary from stuff where stuff.id=id);
    -> end **
#需要设置less safe log_bin_trust_function_creators variable
ERROR 1418 (HY000): This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its declaration and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)
#进行设置
mysql> SET GLOBAL log_bin_trust_function_creators = 1;
    -> **
Query OK, 0 rows affected (0.01 sec)
#创建函数
mysql> create function fun_salary(id int(11))
    -> returns int(11)
    -> begin
    -> return(select salary from stuff where stuff.id=id);
    -> end **
Query OK, 0 rows affected (0.02 sec)
#调用函数
mysql> delimiter ;
mysql> select fun_salary(5);
+---------------+
| fun_salary(5) |
+---------------+
|          3500 |
+---------------+
1 row in set (0.00 sec)

```

##### 变量的使用

1. declare 定义变量

   可以使用declare来定义变量,变量可以在子程序中声明并使用，这些变量的**作用范围是在BEGIN…END程序中**.

   ```Sql
   declare var_name[,...] type [default xxx]
   
   ```

2. 为变量赋值

   ```Sql
   set var_name = expr[,var_name = expr]..
   
   ```

   expr是赋值表达式，一个set可以为多个变量赋值

   还可以使用将select出的值赋给变量

   ```Sql
   select col_name[,...] into var_name[,...]
   from table_name where condition
   
   ```

   例如

   ```Sql
   select salary into var_salary 
   from stuff where id = 3;
   
   ```

##### 定义条件和处理程序

定义条件和处理程序是事先定义了程序可能会出现的问题，并且进行解决。

1. 定义条件

   ```Sql
   declare condition_name condition for condition_value
   
   
   condition_value:
   sqlstate[value] sqlstate_value|mysql_error_code
   
   ```

   参数sqlstate_value和mysql_error_code都可以表示mysql错误

   

2. 定义处理程序

   ```Sql
   delcare handler_type handler for condition_value[,...] proc_statement
   
   #指明错误的处理方式
   handler_type:
   continue|exit|undo
   
   #错误类型
   condition_value:
   sqlstate[value]sqlstate_value|condition_name|sqlwarning|not found|sqlexception|mysql_error_code
   
   ```

   condition_value的取值

   - sqlstate[value]sqlstate_value包含5个字符的字符串错误值
   - condition_name表示declare condition定义的错误名称
   - sqlwarning匹配所有01开头的错误代码
   - not found匹配所有02开头的错误代码
   - sqlexception匹配没有被sqlwarning和not found捕获的错误代码
   - mysql_error_code匹配数值类型错误代码

   > 遇到错误时最好立即停止最好执行退出，因为Mysql现在还不支持undo。

##### 光标的使用

光标的使用包括声明光标，打开光标，使用光标和关闭光标

声明光标 语法：DECLARE 光标名字 CURSOR FOR sql语句

打开光标 OPEN 光标名称

使用光标 FETCH 光标名称 into 。。。

关闭光标 CLOSE 光标名称

1. 声明光标

   ```Sql
   declare cursor_name cursor
   for select statement;
   
   ```

2. 打开光标

   ```Sql
   open cur_name;
   
   ```

3. 使用光标

   ```Sql
   fetch cursor_name into var_name[,var_name];
   
   ```

   var_name必须在光标前定义好

4. 关闭光标

   ```Sql
   close cursor_name;
   
   ```

   关闭后就不能通过fetch来使用光标了。

##### 流程控制的使用

Mysql中可以使用IF，CASE，LOOP，LEAVE，ITERATE，REPEAT，WHILE。

1. if语句

   ```Sql
   if search_condition then statement_list
   [elseif search_condition then statement_list]...
   [else statement_list]
   end if;
   
   ```

   ```Sql
   if
   age>20 then @count1=@count1+1;
   elseif age=20 then @count2=@count2+1;
   else @count3=@count+1;
   end if;
   
   ```

   

2. case语句

   ```Sql
   case case_value
        when when_value|search_condition then statement_list
        [......]
        [else statement_list]
   end case
   
   ```

   when_value表示条件判断变量，  search_condition表示条件判断语句。二选一

3. loop语句

   LOOP可以使某些特定的语句重复执行，实现一个简单的循环，本身没有停止循环，必须遇到leave语句才能停止循环。

   ```Sql
   [begin_label:]LOOP
           statement_list
    end LOOP [end_label]
   
   ```

   begin_label和end_label分别表示循环的开始和停止可以省略。

4. leave语句

   主要用于跳出循环

   ```Sql
   leave label
   
   ```

   ```Sql
   add_num:loop
   set @count=@count+1;
   if @count=100 then
   leave add_num;
   end loop add_num;
   
   ```

5. iterate

   iterate也是跳出循环，和leave的不同是，iterate跳出的是本次循环，而leave跳出的是整个循环。

   ```Sql
   iterate label;
   
   ```

   

6. repeat

   repeat是由条件的循环语句，当满足特定条件就跳出

   ```Sql
   [begin_label:]repeat
      statement_list          #循环语句
      until search_conditon   #结束条件
    end repeat [end_label]
   
   ```

   

7. while

   while是满足条件才循环和repeat是不一样的。

   ```Sql
   [begin_label:]while search_list
       statement_list
    end while [end_label]
   
   ```

#### 存储过程和函数的调用

##### 存储过程的调用

```Sql
call proc_name([proc_param,....]);

```



##### 函数的调用

```Sql
select fun_name([fun_param,...])

```

#### 查看存储过程和函数

1. show  {procedure|function}  status  {like 'proc/func_name'}

2. show create {procedure|function} proc/fun_name

3. 从information_schema.Routines表中查看存储过程和函数的信息

   ```Sql
   select * from infromation_schema.routines 
   where routine_name='proc_name|func_name';
   
   ```

#### 修改存储过程和函数

修改存储过程和函数的语法

```Sql
alter {procedure|function} proc_name[characteristic...];

characteristic:
{contains sql | no sql | reads sql data | modifies sql data}
| sql security{definder|invoker}
|commit'string'

```

```Sql
#修改存储过程proc_salary权限修改为可以写数据，并指明调用者可以执行
mysql> alter procedure proc_salary modifies sql data sql security invoker;
Query OK, 0 rows affected (0.02 sec)


mysql> show procedure status like'proc_salary';
+---------+-------------+-----------+----------------+---------------------+---------------------+---------------+---------+----------------------+----------------------+--------------------+
| Db      | Name        | Type      | Definer        | Modified            | Created             | Security_type | Comment | character_set_client | collation_connection | Database Collation |
+---------+-------------+-----------+----------------+---------------------+---------------------+---------------+---------+----------------------+----------------------+--------------------+
| sqltest | proc_salary | PROCEDURE | root@localhost | 2019-05-12 22:40:09 | 2019-05-12 00:00:51 | INVOKER       |         | gbk                  | gbk_chinese_ci       | utf8mb4_0900_ai_ci |
+---------+-------------+-----------+----------------+---------------------+---------------------+---------------+---------+----------------------+----------------------+--------------------+
1 row in set (0.01 sec)

```

修改函数的语法和存储过程相同。

#### 删除存储过程和函数

1. 删除存储过程

   ```Sql
   dorp procedure proc_name
   
   ```

2. 删除函数

   ```Sql
   drop function func_name
   
   ```

测试：

```Sql
#先调用以前写的存储过程
mysql> call proc_salary;
+--------+
| salary |
+--------+
|   1000 |
|   1500 |
|   2000 |
|   2000 |
|   3500 |
|   1500 |
|   3400 |
|   6000 |
|   7600 |
+--------+
9 rows in set (0.00 sec)

Query OK, 0 rows affected (0.02 sec)

#删除存储过程
mysql> drop procedure proc_salary;
Query OK, 0 rows affected (0.08 sec)

#再次查看
mysql> call proc_salary;
ERROR 1305 (42000): PROCEDURE sqltest.proc_salary does not exist

```

##### 问题与总结

1. 存储过程和存储函数有什么区别？

   - 一般来说，存储过程实现的功能要复杂一点，而函数的实现的功能针对性比较强。       
   - 当对数据库进行复杂操作时(如对多个表进行Update、Insert、Query、Delete时），可将此复杂操作用存储过程封装起来与数据库提供的事务处理结合一起使用。[存储过程可以从自己的存储过程内引用其它存储过程](http://blog.csdn.net/xiaohutushen/article/details/1956429)，这可以简化一系列复杂语句.         
   - 存储过程一般是作为一个独立的部分来执行，而函数可以作为查询语句的一个部分来调用，由于函数可以返回一个表对象，因此它可以在查询语句中位于FROM关键字的后面。
   - 存储过程只在创造时进行编译，以后每次执行存储过程都不需再重新编译，而一般SQL语句每执行一次就编译一次,所以使用存储过程可提高数据库执行速度。
   - 存储过程可以接受参数、输出参数、返回单个或多个结果集以及返回值，可以向程序返回错误原因。但函数只能返回一个特定类型的值或者表对象。      
   - 存储过程中的CRUD的操作会影响数据库状态，但函数却不能。
   - 函数只能是in类型(都是输入参数，结果只能用return返回并且只能返回一个结果)，存储过程可以使用In\out\inout类型（可以返回任意个数）。

2. 存储过程的代码可以改变吗？

   Mysql不存在对存储过程代码修改的方法，因此修改存储过程代码的方法是，先删除原来的存储过程，然后再新创建一个新的存储过程。

3. 存储过程中可以调用其它的存储过程吗？

   可以使用call在存储过程中调用其他的存储过程，但是**不能在里面使用drop来删除其它的存储过程**

4. 存储过程的参数可以和数据表中的字段名相同吗？

   我认为不可以，应该会造成混乱，尽量不要相同

5. 存储过程的参数可以写为中文吗？

   如果传入参数是中文，需要在定义存储过程时，在后边加上character set gbk

   ```Sql
   create procedure procedure_name (IN paramName type ,character set gbk,OUT paraName type type);
   
   ```

6. 存储函数和Mysql内部函数有什么区别？

   从根本上讲存储函数和内部函数是一样的，只不过存储函数是用户自定义来执行SQL并通过return返回的。

## 触发器

### 触发器简介

触发器（trigger）：监视某种情况，并触发某种操作，它是提供给程序员和数据分析员来保证数据完整性的一种方法，它是与表事件相关的特殊的存储过程，它的执行不是由程序调用，也不是手工启动（比如存储过程和存储函数），而是由预定事件来触发，例如当对一个表进行操作（ insert，delete， update）时就会激活它执行。触发器可以算是一个特殊的存储过程。

### 触发器的创建

语法

```Sql
create trigger trigger_name
before|after trigger_event
on table_name for each row trigger_action

#####################################################

create trigger triggerName
after/before insert/update/delete on 表名
for each row   #这句话在mysql是固定的
#如果是多条语句，需要用begin，end包裹
begin
sql语句;
end

```

- before|after指定了触发器在触发事件前|后执行除法语句。
- trigger_event是触发事件
- for each row表示的是任何一条记录上操作满足触发事件都会触发此触发器
- trigger_action是触发事件前|后被执行的语句

### 查看触发器状态

1. show triggers \G

2. ```sql
   use information_schema;
   select * from triggers \G
   
   ```

### 删除触发器

```Sql
drop trigger trigger_name;

```

### 触发器案例

新建学校数据库，里面包含三个表，学生表，班级表，成绩表。

每插入一个学生，对应班级studentCount+1，反则，-1。

每更新一条成绩数据都会影响到学生表的总分totalScore。

1. 建立基本表

   ```Sql
   mysql> create table t_class(
       -> classId int(4) not null auto_increment primary key,
       -> className varchar(20) not null,
       -> location varchar(40) not null,
       -> advisor varchar(20) not null,
       -> studentCount int(4) default 0);
   Query OK, 0 rows affected (0.15 sec)
   
   mysql> create table t_student(
       -> studentId int(4) auto_increment not null primary key,
       -> studnetName varchar(20) not null,
       -> gender varchar(8) not null,
       -> age int(4) not null,
       -> classId int(4) not null,
       -> totalScore int(4) default 0);
   Query OK, 0 rows affected (0.06 sec)
   
   mysql> create table t_score(
       -> studentId int(4) auto_increment not null primary key,
       -> Chinese int(4) default 0,
       -> Englist int(4) default 0,
       -> Maths int(4) default 0);
   Query OK, 0 rows affected (0.10 sec)
   
   mysql> alter table t_score change Englist English int(4) default 0;
   Query OK, 0 rows affected (0.03 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   ```

2. 建立学生对班级的触发器

   ```Sql
   use sqltest;
   
   delimiter $
   create trigger update_stu_count
   after insert on t_student for each row 
   begin
   update t_class set studentCount=studentCount+1
   where classId=New.classId;
   end $
   delimiter ;
   
   ```

   ![1557655626946](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557655626946.png)

3. 添加数据进行测试

   先添加几个班级

   ```Sql
   use sqltest;
   insert into t_class values
   (1,'class1','loc1','advisor1',0),
   (2,'class2','loc2','advisor2',0),
   (3,'class3','loc3','advisor3',0); 
   
   ```

   然后添加几个学生看看班级的人数会不会发生改变

   ```Sql
   use sqltest;
   insert into t_student values
   (1,'zhangsan','m','18',1,0)
   ,(2,'lisi','f','19',2,0)
   ,(3,'wangwu','m','15',3,0);
   
   ```

   然后查看班级表有没有人数变化,发现每个班各有一个人

   ```Sql
   mysql> select * from t_class;
   +---------+-----------+----------+----------+--------------+
   | classId | className | location | advisor  | studentCount |
   +---------+-----------+----------+----------+--------------+
   |       1 | class1    | loc1     | advisor1 |            1 |
   |       2 | class2    | loc2     | advisor2 |            1 |
   |       3 | class3    | loc3     | advisor3 |            1 |
   +---------+-----------+----------+----------+--------------+
   3 rows in set (0.00 sec)
   
   
   ```

4. 创建成绩和总分之间触发器

   ```Sql
   use sqltest;
   delimiter $
   create trigger update_total_score 
   after insert on t_score for each row
   begin
   update t_student set totalScore=New.Chinese+New.English+New.Maths 
   where  studentId=New.studentId;
   end $
   
   ```

   插入几条成绩数据查看效果

   ```Sql
   use sqltest;
   insert into t_score values(1,80,90,70);
   
   
   
   mysql> select * from t_student;
   +-----------+-------------+--------+-----+---------+------------+
   | studentId | studnetName | gender | age | classId | totalScore |
   +-----------+-------------+--------+-----+---------+------------+
   |         1 | zhangsan    | m      |  18 |       1 |          0 |
   |         2 | lisi        | f      |  19 |       2 |          0 |
   |         3 | wangwu      | m      |  15 |       3 |          0 |
   +-----------+-------------+--------+-----+---------+------------+
   3 rows in set (0.00 sec)
   
   mysql> select * from t_score;
   +-----------+---------+---------+-------+
   | studentId | Chinese | English | Maths |
   +-----------+---------+---------+-------+
   |         1 |      80 |      90 |    70 |
   +-----------+---------+---------+-------+
   1 row in set (0.00 sec)
   
   mysql> select * from t_student;
   +-----------+-------------+--------+-----+---------+------------+
   | studentId | studnetName | gender | age | classId | totalScore |
   +-----------+-------------+--------+-----+---------+------------+
   |         1 | zhangsan    | m      |  18 |       1 |        240 |
   |         2 | lisi        | f      |  19 |       2 |          0 |
   |         3 | wangwu      | m      |  15 |       3 |          0 |
   +-----------+-------------+--------+-----+---------+------------+
   3 rows in set (0.00 sec)
   
   ```

   但是这样并不是完美的，因为在更新数据的时候也会造成总分变化，因此需要增加一个更新数据时的触发器

   ```Sql
   use sqltest;
   
   delimiter ;
   delimiter $
   create trigger update_score_total
   after update on t_score for each row
   begin 
   update t_student set totalScore=New.Chinese+New.Maths+New.English
   where studentId=New.studentId;
   end $
   
   
   ```

   然后再试试更新score

   ```Sql
   mysql> select * from t_student;
   +-----------+-------------+--------+-----+---------+------------+
   | studentId | studnetName | gender | age | classId | totalScore |
   +-----------+-------------+--------+-----+---------+------------+
   |         1 | zhangsan    | m      |  18 |       1 |        240 |
   |         2 | lisi        | f      |  19 |       2 |          0 |
   |         3 | wangwu      | m      |  15 |       3 |          0 |
   +-----------+-------------+--------+-----+---------+------------+
   3 rows in set (0.00 sec)
   
   mysql> update t_score set Chinese=50 where studentId=1;
   Query OK, 1 row affected (0.02 sec)
   Rows matched: 1  Changed: 1  Warnings: 0
   
   mysql> select * from t_student;
   +-----------+-------------+--------+-----+---------+------------+
   | studentId | studnetName | gender | age | classId | totalScore |
   +-----------+-------------+--------+-----+---------+------------+
   |         1 | zhangsan    | m      |  18 |       1 |        210 |
   |         2 | lisi        | f      |  19 |       2 |          0 |
   |         3 | wangwu      | m      |  15 |       3 |          0 |
   +-----------+-------------+--------+-----+---------+------------+
   3 rows in set (0.00 sec)
   
   ```

   这样就很轻松愉快的使用触发器进行自动更新操作了！

### 问题与总结

1. 可不可以给一个表上创建多个触发器？

   当然可以，但是这个可以是有一定限制的，相同的事件只能创建一个触发器，也就是说如果给total创建了一个before insert 触发器，如果再给它创建一个before insert触发器就会报错。

2. 是否需要及时删除不用的触发器？

   需求变更的话，如果不删除就的触发器就有可能会使新的数据完整性遭到破坏。

3. 说明一下delimiter，是因为在begin end内要使用分号结束，因此就使用delimiter来暂时改变结束标志。

## 事务和锁

### 事务的概念

所谓事务是用户定义的一个数据库操作系列，这些操作要么全部执行，要么全部不执行，是一个不可分割的工作单位。例如在关系数据库中，一个事务可以是一条sql语句、一组sql语句或整个程序。

### 事务的ACID四大特性

- 原子性（Atomicity）：事务中的规定的一系列操作都是一个原子单元，要么成功，要么失败。
- 一致性（Consistency）：事务在完成时，必须是从一种一致性状态到另一种一致性状态，确保数据完整性。
- 隔离性（Isolution）：一个事务的执行所做的修改必须与其他事务所做的修改相互隔离。
- 持久性（Durability）：事务完成后，所做的修改对数据的影响是永久的，也就是说如果重启或系统故障也可以恢复。

![1557660178551](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1557660178551.png)



Mysql中提供了多种事务型存储引擎，如InnoDB，BDB等，MyISAM不支持事务，InnoDB支持ACID事务，行级锁，高并发等。为了更好的支持事务，InnoDB引入了与事务相关的REDO和UNDO日志。

### 和事务相关的日志

#### REDO

作用：确保事务的持久性。防止在发生故障的时间点，尚有脏页（在缓冲区中修改还没刷新到磁盘）未写入磁盘，在重启mysql服务的时候，根据redo log进行重做，从而达到事务的持久性这一特性。

内容：物理格式的日志，记录的是物理数据页面的修改的信息，其redo log是顺序写入redo log file的物理文件中去的。

什么时候产生：事务开始之后就产生redo log，redo log的落盘并不是随着事务的提交才写入的，而是在事务的执行过程中，便开始写入redo log文件中。

对应的物理文件：data目录下ib_logfile*。

关于文件的大小和数量，由以下两个参数配置：
innodb_log_file_size 重做日志文件的大小。
innodb_mirrored_log_groups 指定了日志镜像文件组的数量，默认1

上面提到的Dirty page：脏页 什么意思呢？
       一般业务运行过程中，当业务需要对某张的某行数据进行修改的时候，innodb会先将该数据从磁盘读取到缓存中去，然后在缓存中对这条数据进行修改，这样缓存中的数据就和磁盘的数据不一致了，这个时候缓存中的数据就称为dirty page，只有当脏页统一刷新到磁盘中才会是clean page（干净页）。
     一般情况下，mysql在崩溃之后，重启服务，innodb通过回滚日志undo将所有已完成并写入磁盘的未完成事务进行rollback，然后redo中的事务全部重新执行一遍即可恢复数据，但是随着redo的量增加，每次从redo的第一条开始恢复就会浪费长的时间，所以引入了checkpoint机制。

Checkpoint：如果在某个时间点，脏页的数据被刷新到了磁盘，系统就把这个刷新的时间点记录到redo log的结尾位置，在进行恢复数据的时候，checkpoint时间点之前的数据就不需要进行恢复了，可以缩短时间。

#### UNDO

作用：一般是用来事务发生异常时进行回滚。具体做的就是复制事务执行前的数据库内容到UNDO缓冲区，然后在合适的事件刷入到磁盘。

什么时候产生：事务开始之前，将当前是的版本生成undo log，undo 也会产生 redo 来保证undo log的可靠性

和REDO的区别，磁盘不单独存在UNDO日志文件，所有的UNDO日志文件都放在表空间对应的.ibd文件中。UNDO日志也被称为回滚段。

### Mysql事务控制

事务控制语句语法格式

```Sql
start transaction | begin [work]
commit [work] [and [no] chain] [[no] release]
rollback [work] [and [no] chain] [[no] release]
set autocommit = {0|1}

```

默认设置下，Mysql是自动提交的。如果需要事务控制不用修改提交方式，可以使用start transaction| begin开始一个事务，这样事务结束后还可以回到自定提交的状态。

比如:

```Sql
use sqltest;

begin;
insert into class01 values(5,'一班','xxx');
commit;
select * from class01;

mysql> select * from class01;
+---------+-------+----------+
| classno | cname | location |
+---------+-------+----------+
|       5 | 一班  | xxx      |
+---------+-------+----------+
1 row in set (0.00 sec)

```

### Mysql事务隔离级别

Mysql8.0查看事务隔离级别

```Sql
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+
1 row in set (0.00 sec)

```

#### 由于隔离级别会引起的几种异常

##### 脏读

脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。

##### 不可重复读

不可重复读就是在**一个事务内多次读取统一数据产生不同的结果**。比如事务A的两次读取数据之间，事务B修改了此数据而导致两次读出来的数据不一致。

##### 幻读

幻读就是事务A第一次读出的符合条件的数据有N条，在第一次和第二次之间事务B**插入**了N条符合条件的数据，然后事务A中的第二次读出的符合条件的数据有N+1条。

又比如说是，事务A修改了当前表的所有数据，同时事务B添加了一条记录，后来操作事务A的用户发现还有一条记录没有被修改。

#### 读未提交（READ-UNCOMMITED）

在此隔离级别，所有事务都可以看到其它未提交事务的执行结果。读未提交的数据成为脏读。

#### 读已提交（READ-COMMITED）

这是大多数系统默认的隔离级别，但是Mysql默认的是可重复读，它定义了事务只能看见已提交事务所做的改变。在处理期间可能会有新的数据提交导致数据改变，可能会有不可重复读。

#### 可重读（REPEATABLE-READ）

是Mysql默认的隔离级别，能确保同一事务的多个实例在并发读取数据时会看到同样的数据行，理论上会有幻读问题，但是InnoDB和Falcon存储引擎通过多版本并发控制（Multi_Version_Concurency_Control,MVCC）机制已经解决了改问题。

#### 可串行化（SERIALIZABLE）

这是最高的隔离级别，通过强制的事务排序，不可能相互冲突，也完全解决了幻读问题。其原理就是在每一个读的数据行加上共享锁，因此可能在此级别会导致大量的超时等待和竞争。

### 锁

#### 锁的几种类型

**行锁：**

##### 共享锁（Share，S）

允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁（也就是不允许其他事务进行写对此区域进行写操作）。

级别是行或者元组（多个行），也就是行级锁。

##### 排他锁（eXclusive，X）

一个事务获取到排他锁后可以对此区域进行写操作，别的事务不能进来。

级别是行或者元组（多个行），也是行级锁。

**表锁：**

为了允许行锁和表锁共存，实现多粒度锁机制，InnoDB还有两种内部使用的意向锁（Intention Locks），这两种意向锁都是表锁。

##### 意向锁

意向锁又分为意向共享锁和意向排他锁。

###### 意向共享锁（IS）

事务打算给数据行加行共享锁，事务在给一个数据行加共享锁前必须先取得该表的IS锁。也就是说有加共享锁的意图。

###### 意向排他锁（IX）

事务打算给数据行加行排他锁，事务在给一个数据行加排他锁前必须先取得该表的IX锁。也就是说有加排他锁的意图。

##### InnoDB行锁模式兼容性列表

| 请求锁模式    是否兼容 当前锁模式 | X    | IX   | S    | IS   |
| --------------------------------- | ---- | ---- | ---- | ---- |
| X                                 | 冲突 | 冲突 | 冲突 | 冲突 |
| IX                                | 冲突 | 兼容 | 冲突 | 兼容 |
| S                                 | 冲突 | 冲突 | 兼容 | 兼容 |
| IS                                | 冲突 | 兼容 | 兼容 | 兼容 |

**为尽可能提高数据库的并发，锁的粒度要越小，越小的锁耗费的资源越多，系统性能越低**。需要在性能和并发中进行平衡，这也就是锁粒度产生的原因。

