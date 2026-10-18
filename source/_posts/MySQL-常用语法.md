---
title: "MySQL 常用语法"
date: 2022-08-12 14:41:03
---
## 一.DDL

### 1.创建数据库

```plaintext
create database 数据库名称;
```

### 2.选择数据库语法

```plaintext
use 数据库名称;
```

### 3.显示数据库中所有表语法

```plaintext
show tables;
```

### 4.删除数据库语法

```plaintext
drop database 数据库名称;
```

### 5.创建表语法

```plaintext
create table 表名(
列名1 列的类型
列名2 列的类型
列名3 列的类型

);
```

### 6.常用数据类型

#### 数值类型

| SMALLINT | 2 字节 | (-32 768，32 767) | (0，65 535) | 大整数值 |
| --- | --- | --- | --- | --- |
| MEDIUMINT | 3 字节 | (-8 388 608，8 388 607) | (0，16 777 215) | 大整数值 |
| INT或INTEGER | 4 字节 | (-2 147 483 648，2 147 483 647) | (0，4 294 967 295) | 大整数值 |
| BIGINT | 8 字节 | (-9 233 372 036 854 775 808，9 223 372 036 854 775 807) | (0，18 446 744 073 709 551 615) | 极大整数值 |
| FLOAT | 4 字节 | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38) | 单精度 浮点数值 |
| DOUBLE | 8 字节 | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
| DECIMAL | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值 | 依赖于M和D的值 | 小数值 |

#### 日期和时间类型

| 类型 | 大小 (字节) | 范围 | 格式 | 用途 |
| --- | --- | --- | --- | --- |
| DATE | 3 | 1000-01-01/9999-12-31 | YYYY-MM-DD | 日期值 |
| TIME | 3 | ‘-838:59:59’/‘838:59:59’ | HH:MM:SS | 时间值或持续时间 |
| YEAR | 1 | 1901/2155 | YYYY | 年份值 |
| DATETIME | 8 | 1000-01-01 00:00:00/9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值 |
| TIMESTAMP | 4 | 1970-01-01 00:00:00/2038 结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038-1-19 03:14:07 | YYYYMMDD HHMMSS | 混合日期和时间值，时间戳 |

#### 字符串类型

| 类型 | 大小 | 用途 |
| --- | --- | --- |
| CHAR | 0-255字节 | 定长字符串 |
| VARCHAR | 0-65535 字节 | 变长字符串 |
| TINYBLOB | 0-255字节 | 不超过 255 个字符的二进制字符串 |
| TINYTEXT | 0-255字节 | 短文本字符串 |
| BLOB | 0-65 535字节 | 二进制形式的长文本数据 |
| TEXT | 0-65 535字节 | 长文本数据 |
| MEDIUMBLOB | 0-16 777 215字节 | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT | 0-16 777 215字节 | 中等长度文本数据 |
| LONGBLOB | 0-4 294 967 295字节 | 二进制形式的极大文本数据 |
| LONGTEXT | 0-4 294 967 295字节 | 极大文本数据 |

提示：CHAR 和 VARCHAR 类型类似，但它们保存和检索的方式不同。它们的最大长度和是否尾部空格被保留等方面也不同。在存储或检索过程中不进行大小写转换。简单说：CHAR类型检索效率高，但是是定长，VARCHAR类型检索效率低，但是是变长。

### 7.显示表结构（desc语法）

```plaintext
desc 表名;
```

### 8.删除表

```plaintext
drop table 表名;
```

### 9.修改列的类型语法

```plaintext
alter table 表名 modify 列名 列的类型;
```

### 10.添加列语法

```plaintext
alter table 表名 add column 列名 类型;
```

### 11.删除表的列

```plaintext
alter table 表名 drop 列名;
```

### 12.修改列的名称

```plaintext
alter table 表名 change 原始名称 新的名称 列的类型;
```

### 13.表改名

```plaintext
alter table 表名 rename 新表名;
```

## 二.DML语句

```plaintext
DML是操作对数据库中表记录的操作
主要表的记录操作有：
插入(insert)
查询(select)
删除(delete)
更改(update)
```

### 1.插入(insert)一次添加多条

```plaintext
insert into tablename(列名1，列名2.....列名n)
,(值1，值2.......值n),
,(值1，值2.......值n),
,(值1，值2.......值n);
#对于含可空列的，非空但是含有默认值的字段，自增字段，可以不用在insert后添加列名,values后面直接对应列就行
```

### 2.更改(update)

```plaintext
update 表名 set 列名1 = 值1，列名2 = 值2...列名n = 值n;
#对表中的数据，可以通过update命令进行修改,一般都会加上where语句
```

### 3.删除(delete)

```plaintext
delete from tablename [where condition]
```

### 4.查询(select)

#### 查询

```plaintext
select * from 表名;
```

```plaintext
select name,sal from emp;
```

#### 查询不重复的记录

```plaintext
select distinct 列名 from 表名;
```

#### 条件查询

逻辑运算符：

```plaintext
=,>,<,>=,<=,!=,<>
```

```plaintext
select * from dept where deptno > 2;
```

#### 聚合函数

聚合函数：

| 函数名 | 功能 |
| --- | --- |
| count(列名) | 统计数量 |
| max(列名) | 最大值 |
| min(列名) | 最小值 |
| sum(列名) | 求和 |
| avg(列名) | 平均值 |

聚合函数语法：

```plaintext
select 聚合函数名(列名) from 表;
```

#### 分组查询

```plaintext
select 字段列表 from 表名 [where 分组前条件限定] group by 分组字段名 [having 分组后条件过滤];
```

注意：分组之后，查询的字段为聚合函数和分组字段，查询其他字段无任何意义。

where 和having区别：

执行时机不一样：where是分组之前进行限定，不满足where条件，则不参与分组，而having是分组之后对结果进行过滤。

可判断的条件不一样：where不能对聚合函数进行判断，having可以

执行顺序：where>聚合函数>having

#### 分页查询

```plaintext
select 字段列表 from 表名 limit 起始索引，查询条目数;
```

起始索引从0开始

#### 排序查询

```plaintext
select * from 表名 [where 条件] [order by 列名1[desc|asc],列名2 [desc|asc]....列名n [desc|asc]]

desc : 降序
asc : 升序
```

## 三.DCL语句

### 1.主要是DBA用来管理系统中的对象权限.

```plaintext
创建一个数据库用户wuyu,具有对lq数据库中所有表的查询和添加权限.
mysql> grant select,insert on lq.* to 'wuyu'@'localhost' identified by '123';
Query OK, 0 rows affected, 1 warning (0.22 sec)

C:\Users\Administrator>mysql -uwuyu -p123
mysql -u用户名 -p密码
```
