---
title: "PHP反序列化"
date: 2022-05-08 16:18:03
---
[[CTFShow-web入门-PHP反序列化]]

# PHP反序列化

## 简介:

序列化其实就是将数据转化成一种可逆的数据结构，自然，逆向的过程就叫做反序列化。

PHP将数据序列化有两个函数：

**serialize** 将对象格式化成有序的字符串。

**unserialize** 将字符串还原成原来的对象。

序列化的目的是方便数据的传输和存储，在PHP中，序列化和反序列化一般用做缓存，比如session缓存，cookie等。

## 反序列化的注意事项：

将数据进行序列化的操作会自动忽略空格所以进行反序列化之后如果变量前是protected，则会在变量名前加上`\x00*\x00`,private则会在变量名前加上`\x00类名\x00`,输出时一般需要url编码，若在本地存储更推荐采用base64编码的形式，输出则会导致不可见字符`\x00`的丢失。

## 反序列化中常见的魔术方法：

```php
__sleep() //执行serialize()时，先会调用这个函数
__destruct() //对象被销毁时触发
__call() //在对象上下文中调用不可访问的方法时触发
__callStatic() //在静态上下文中调用不可访问的方法时触发
__get() //用于从不可访问的属性读取数据或者不存在这个键都会调用此方法
__set() //用于将数据写入不可访问的属性
__isset() //在不可访问的属性上调用isset()或empty()触发
__unset() //在不可访问的属性上使用unset()时触发
__toString() //把类当作字符串使用时触发
__invoke() //当尝试将对象调用为函数时触发
__destruct(类执行完毕以后调用，其最主要的作用是拿来做垃圾回收机制。)
__construct(类一执行就开始调用，其作用是拿来初始化一些值。)
__toString(在对象当做字符串的时候会被调用。)
__wakeup(该魔术方法在反序列化的时候自动调用，为反序列化生成的对象做一些初始化操作)
__sleep(在对象被序列化的过程中自动调用。sleep要加数组)
__invoke(当尝试以调用函数的方式调用一个对象时，方法会被自动调用)
__get(当访问类中的私有属性或者是不存在的属性，触发__get魔术方法)
__set(在对象访问私有成员的时候自动被调用，达到了给你看，但是不能给你修改的效果！在对象访问一个私有的成员的时候就会自动的调用该魔术方法)
__call(当所调用的成员方法不存在（或者没有权限）该类时调用，用于对错误后做一些操作或者提示信息)
```

## 反序列化绕过方法：

### php7.1+反序列化对类属性不敏感

我们前面说了如果变量前是protected，序列化结果会在变量名前加上`\x00*\x00`，但在特定版本7.1以上则对于类属性不敏感，

### 绕过__wakeup(CVE-2016-7124)

```plaintext

版本：

​ PHP5 < 5.6.25

​ PHP7 < 7.0.10
```

利用方式：`序列化字符串中表示对象属性个数的值大于真实的属性个数时会跳过__wakeup的执行`

对于下面一个自定义类：

```php
<?php
class test{
    public $a;
    public function __construct(){
        $this->a = 'abc';
    }
    public function __wakeup(){
        $this->a='123';
    }
    public function  __destruct(){
        echo $this->a;
    }
}
```

如果执行`unserialize('O:4:"test":1:{s:1:"a";s:3:"abc";}');`输出结果为`123`

而把对象属性个数的值增大执行`unserialize('O:4:"test":2:{s:1:"a";s:3:"abc";}');`输出结果为`abc`

### 利用引用

```php
<?php
class test{
    public $a;
    public $b;
    public function __construct(){
        $this->a = 'abc';
        $this->b= &$this->a;
    }
    public function  __destruct(){

        if($this->a===$this->b){
            echo 123;
        }
    }
}
$a = serialize(new test());
```

上面这个例子将`$b`设置为`$a`的引用，可以使`$a`永远与`$b`相等

### [16进制](https://so.csdn.net/so/search?q=16%E8%BF%9B%E5%88%B6&spm=1001.2101.3001.7020)绕过字符的过滤

```php
O:4:"test":2:{s:4:"%00*%00a";s:3:"abc";s:7:"%00test%00b";s:3:"def";}
可以写成
O:4:"test":2:{S:4:"\00*\00\61";s:3:"abc";s:7:"%00test%00b";s:3:"def";}
表示字符类型的s大写时，会被当成16进制解析。
```

```plaintext
<?php
class test{
    public $username;
    public function __construct(){
        $this->username = 'admin';
    }
    public function  __destruct(){
        echo 666;
    }
}
function check($data){
    if(stristr($data, 'username')!==False){
        echo("你绕不过！！".PHP_EOL);
    }
    else{
        return $data;
    }
}
// 未作处理前
$a = 'O:4:"test":1:{s:8:"username";s:5:"admin";}';
$a = check($a);
unserialize($a);
// 做处理后 \75是u的16进制
$a = 'O:4:"test":1:{S:8:"\\75sername";s:5:"admin";}';
$a = check($a);
unserialize($a);
```

参考链接：<https://blog.csdn.net/solitudi/article/details/113588692>
