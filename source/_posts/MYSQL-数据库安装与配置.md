---
title: "MYSQL 数据库安装与配置"
date: 2022-08-09 20:52:46
---
# MYSQL数据库配置

## 下载

[下载地址](https://dev.mysql.com/downloads/mysql/)

根据需求下载好文件

解压

## 配置环境变量

记录下解压文件的地址

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-09205811.png)

点击设置->系统->高级系统设置->环境变量

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-09210010.png)

新建，设置环境变量

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-09210211.png)

点击下面的Path，新增以下变量：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-09210430.png)

## 配置my.ini

在解压目录下新建my.ini

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-0210639.png)

输入以下内容：

```plaintext
[mysql]
default-character-set=utf8

[mysqld]
character-set-server=utf8
default-storage-engine=INNODB
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

## 运行

以管理员身份打开CMD

输入：

```plaintext
mysqld --initialize-insecure
```

若执行命令成功会在解压目录下生成data的文件夹，若失败请将my.ini文件放入bin目录下再运行

## 注册服务

CMD中输入 `mysqld -install`

## 启动服务

```plaintext
net start mysql //启动mysql服务
net stop mysql  //停止mysql服务
```

## 修改默认的账户密码

在命令行中输入 `mysqladmin -u root password 1234`,这里的 `1234`就是指默认管理员(root账户)的密码，也可以自行修改为自己喜欢的

```plaintext
mysqladmin -u root password 1234
```

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-08-09211934.png)

至此配置完成。

## 登录

输入：

```plaintext
mysql -uroot -p密码
```

即可登录
