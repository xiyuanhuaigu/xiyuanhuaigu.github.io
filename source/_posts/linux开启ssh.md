---
title: "linux开启ssh"
date: 2023-01-13 16:46:03
---
## 1.查看是否安装了[openssh](https://so.csdn.net/so/search?q=openssh&spm=1001.2101.3001.7020)-server

执行命令：

```plaintext
yum list installed | grep openssh-server
```

如果有openssh-server，则是已安装，如果没有则需要安装，执行安装命令：

```plaintext
yum install openssh-server
```

## 2.打开sshd配置文件sshd_config

执行命令：

```plaintext
vim /etc/ssh/sshd_config 
```

去掉监听端口、地址前的注释：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230113165527.png)

开启远程登录：

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230113165641.png)

开启用户密码作为连接验证，保存退出

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230113165732.png)

## 3.开启 sshd 服务

执行命令：

```plaintext
sudo service sshd start 
```

停止SSH服务命令:

```plaintext
service sshd stop
```

重启SSH服务命令:

```plaintext
service sshd restart
```

## 4.设置SSH服务为开机启动。

输入命令：

```plaintext
chkconfig sshd on
若是chkconfig sshd off则禁止SSH开机启动
```
