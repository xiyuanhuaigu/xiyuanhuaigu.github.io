---
title: "CTFshow-信息收集"
date: 2022-07-04 15:38:50
---
# web 1

直接查看源代码即可得到flag

# web2

本题禁用了右键和f12,通过`Ctrl+u`或者在url前面添加`view-source:`也可获得`flag`

# web3 响应头

右键查看源代码 ，什么都没有发现，直接抓包，在响应包中发现flag

# web4 robots.txt

访问robots.txt发现/flagishere.txt。访问flagishere.txt得到flag.

# web5 index.phps

访问index.phps得到flag

phps文件就是php的源代码文件，通常用于提供给用户（访问者）查看php代码，因为用户无法直接通过Web浏览器看到php文件的内容，所以需要用phps文件代替。其实，只要不用php等已经在服务器中注册过的MIME类型为文件即可，但为了国际通用，所以才用了phps文件类型。

它的MIME类型为：text/html, application/x-httpd-php-source, application/x-httpd-php3-source。

# web6 [www.zip](http://www.zip/)

尝试一下常见的源码包名字`www.zip`，即可下载压缩包，在压缩包中发现flag,但提交是错的，访问一下fl000g.txt这个地址，发现了真正的flag

# web7 git

版本控制器，不是`git泄露`就是`svn泄露`

此题通过访问`/.git/`即可获得`flag`

# web8 svn

版本控制器，不是`git泄露`就是`svn泄露`

此题通过访问`/.svn/`即可获得`flag`

# web9 vim缓存

通过访问`index.php.swp`，访问首页中的交换文件下载缓存文件

vim在编辑文档的过程中如果异常退出，会产生缓存文件，第一次产生的缓存文件后缀为.swp，后面会产生swo等.

<https://blog.csdn.net/qq_41497476/article/details/106992025>

# web10 cookie

通过观察网络数据包中的cookie发现flag

# web11 域名解析

发现ctfshow.com隐藏了一条信息，通过常用的域名解析网址`dbcha.com`找到TXT中有flag

# web12 公开信息

在页面最下端发现电话号码

通过访问admin尝试进行管理员账号登录，账号填写`admin`，密码则是电话号码

即可获取`flag`

# web13 技术文档敏感信息

页面滑道最下方发现小写的`document`

访问即可获得一些敏感信息

通过访问`/system1103/login.php`，并用其泄露的账号密码进行登录

即可获得`flag`

# web14 editor

根据题目访问`/editor/`

在其图片空间中找到flag路径

`/editor/attached/image/var/www/html/nothinghere/fl000g.txt`

通过访问`/nothinghere/fl000g.txt`即可获得`flag`

# web15 邮箱地址

题目提示`公开的信息比如邮箱，可能造成信息泄露，产生严重后果`

我们滑到页面最底部发现其邮箱：`1156631961@qq.com`

访问admin进入后台登录系统

点击忘记密码，填写其qq所在地西安，即可重置密码

再通过账号`admin`,密码`admin7789`即可获取`flag`

# web16 tz.php

题目提示`对于测试用的探针，使用完毕后要及时删除，可能会造成信息泄露`

通过提示我们直接访问`tz.php`

然后进一步访问`phpinfo`即可获得`flag`

# web17 sql文件泄露

访问backup.sql

在下载的文件里找到flag

# web18

在JS中找到

```plaintext
if(score>100)
{
var result=window.confirm("\u4f60\u8d62\u4e86\uff0c\u53bb\u5e7a\u5e7a\u96f6\u70b9\u76ae\u7231\u5403\u76ae\u770b\u770b");
}
```

进行Unicode解码

根据谐音提示访问`110.php`,获得`flag`

# web19 前端密钥

通过访问源码，发现其账号密码

通过`post传参`绕过前端验证获取`flag`

# web20 mdb文件

根据题目提示访问路径`/db/db.mdb`，下载`mdb文件`

通过访问`db.mdb`查找关键词`flag`，获得最终`flag`
