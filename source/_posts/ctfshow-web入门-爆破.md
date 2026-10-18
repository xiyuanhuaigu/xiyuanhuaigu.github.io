---
title: "ctfshow web入门 爆破"
date: 2022-07-06 19:39:31
---
# web21

我们随便输入并使用bs抓包得

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-06214010.png)

通过对这串进行base64解码得此处就是我们输入的账号与密码，形式为：账号：密码

我们使用BS中的测试器模块

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/2.png)

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/3.png)

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/1.png)

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-06214809.png)

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-06214913.png)

开始攻击

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-06215517.png)

得到flag

# web 22

现在子域名无法爆破出来，直接查看hint得到flag

# web 23

分析源码，我们可以知道token首先被md5进行加密，而且它的第一位=第14位=第17位，（第一位+第十四位+第十七位）/第一位=第三十一位

```php
<?php

error_reporting(0);

include('flag.php');
if(isset($_GET['token'])){
    $token = md5($_GET['token']);
    if(substr($token, 1,1)===substr($token, 14,1) && substr($token, 14,1) ===substr($token, 17,1)){
        if((intval(substr($token, 1,1))+intval(substr($token, 14,1))+substr($token, 17,1))/substr($token, 1,1)===intval(substr($token, 31,1))){
            echo $flag;
        }
    }
}else{
    highlight_file(__FILE__);

}
?>
```

写一个简单的php爆一下

```php
<?php
for($v1=0;$v1<10;$v1++)
    for($v2=0;$v2<10;$v2++)
        for($v3=0;$v3<10;$v3++){
            $v=$v1.$v2.$v3;
            $token = md5($v);
            if (substr($token, 1, 1) === substr($token, 14, 1) && substr($token, 14, 1) === substr($token, 17, 1)) {
                if ((intval(substr($token, 1, 1)) + intval(substr($token, 14, 1)) + substr($token, 17, 1)) / substr($token, 1, 1) === intval(substr($token, 31, 1))) {
                    echo $v;
                }
            }
        }

?>
```

结果是422

# web 24

通过分析源代码我们可以知道

```php
<?php

error_reporting(0);
include("flag.php");
if(isset($_GET['r'])){
    $r = $_GET['r'];
    mt_srand(372619038);
    if(intval($r)===intval(mt_rand())){
        echo $flag;
    }
}else{
    highlight_file(__FILE__);
    echo system('cat /proc/version');
}

?>
```

PHP随机数的漏洞：

由于mt_rand()的生成的随机数只跟seed和调用该函数的次数有关。举一个简单的例子来说明一下这个问题，假设使用· mt_srand(1111111)进行了一次播种操作，接下来调用mt_rand()函数，第一次生成的数值为a,第二次生成的为b，第三次生成的为c。任何一个人拿到这样的一串代码，所执行的结果都是跟刚刚描述的一样。所以当你的seed数值被他人知道后，就可以预测出你接下来的数值是多少，这就是该函数的一个问题，他并不能起到一个真随机数的作用。

唯一可能出问题的点就是PHP的版本，不同的版本在相同的种子下给的随机数也不同，因此PHP的大版本要相同，然后

```plaintext
<?php
mt_srand(372619038);
echo intval(mt_rand());
```

# web 25

**一.涉及工具：**  
 php_mt_seed[下载地址](https://github.com/openwall/php_mt_seed)

此脚本用于伪随机数漏洞，生成伪随机数是线性的，你可以理解为y=ax,x就是种子，知道种子和一组伪随机数不是就可以推y(伪随机数),当然实际上更复杂肯定。

我知道种子后，可以确定你输出伪随机数的序列。

知道你的随机数序列，可以确定你的种子。

用到的是爆破，已经有写好的C脚本了。

这里简单的介绍下这个脚本咋用

kali下，进入目录，make

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-07175302.png)

使用 `time ./php_mt_seed-main 随机数`

来推出种子

**二.解题思路**

```plaintext
<?php
error_reporting(0);
include("flag.php");
if(isset($_GET['r'])){
    $r = $_GET['r'];
    mt_srand(hexdec(substr(md5($flag), 0,8)));
    $rand = intval($r)-intval(mt_rand());  //intval()用于获取变量的整数值
    if((!$rand)){          //只有rand=0的时候才会执行以下语句，所以我们采用r =0;
        if($_COOKIE['token']==(mt_rand()+mt_rand())){
            echo $flag;
        }
    }else{
        echo $rand;
    }
}else{
    highlight_file(__FILE__);
    echo system('cat /proc/version');
} 
```

1.分析php代码，可以先通过r=0 得到mt_rand()第一个值为644268232（每个人得到的数字不同）

2.之后我们进行逆推，推出种子

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-07180220.png)

3.通过如下代码，我们知道要将两个随机数相加得token值

`if($_COOKIE[‘token’]==(mt_rand()+mt_rand()))`

通过响应头可知PHP的版本为7.311

4.经过尝试我们得到种子和token

```plaintext
<?php
mt_srand(2835652841);
echo mt_rand()."\n";
echo mt_rand()+mt_rand();

644268232
2187070716
```

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-07182019.png)

得到flag

# web 26

在数据库页面发现代码，对传参没有要求

```plaintext
<script>

		function check(){
			$.ajax({
			url:'checkdb.php',
			type: 'POST',
			dataType:'json',
			data:{
				'a':$('#a').val(),
				'p':$('#p').val(),
				'd':$('#d').val(),
				'u':$('#u').val(),
				'pass':$('#pass').val()
			},
			success:function(data){
				alert(data['msg']);
			},
			error:function(data){
				alert(data['msg']);
			}

		});
		}

	</script>
```

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-07183931.png)

得到flag

# web 27

1.下载录取名单

2.通过查询系统爆破

3.可以发现中间正好少了出生年月日

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/04b11cc80d186e6b872e43108ce09846.png)

4.成功后看回显,知道学号以及密码为身份证号码

5.登录得到flag

# web 28

1.通过原始url知道爆破目录

爆破得到flag
