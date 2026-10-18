---
title: "kali子系统配置"
date: 2022-07-27 17:22:36
---
## 准备：

1. 在设置中开启开发人员模式：设置-windows更新-开发者选项-打开开发人员模式
2. 设置搜索并打开控制面板-程序-启用或关闭windows功能-勾选适用于linux的windows系统-确定

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/屏幕截图2022-07-27172608.png)

由于本机是专业版所以需要另外勾选Hyper-v

## 安装：

1. 搜索或打开microsoft store(微软应用商店) 注：关闭自动更新后将不能使用，如需使用请开启检查更新
2. 搜索kali linux下载
3. 启动并设置密码

## 移动系统：

1. 使用LxRunOffline  
    [https://github.com/DDoSolitary/LxRunOffline](https://www.riyueen.com/go?_=9cf1400f06aHR0cHM6Ly9naXRodWIuY29tL0REb1NvbGl0YXJ5L0x4UnVuT2ZmbGluZQ==)

   在releases 里面下载最新版，如不能下载可以评论会转到阿里网盘
2. cd 到解压目录

   ```plaintext
   cd D:\lx
   ```
3. 显示已安装系统

   ```plaintext
   .\LxRunOffline.exe list
   ```
4. 将系统迁移到D:\kali

   ```plaintext
   .\LxRunOffline.exe move -n kali-linux -d D:\kali
   ```
5. 查看是否迁移成功

   ```plaintext
   .\LxRunOffline.exe get-dir -n kali-linux
   ```
6. 关于LxRunOffline的更多使用方法可以看下面链接  
    [https://p3terx.com/archives/manage-wsl-with-lxrunoffline.html](https://www.riyueen.com/go?_=8253d08c41aHR0cHM6Ly9wM3RlcnguY29tL2FyY2hpdmVzL21hbmFnZS13c2wtd2l0aC1seHJ1bm9mZmxpbmUuaHRtbA==)

注：若出现报错可以尝试以下版本：

下载链接：<https://wwt.lanzouq.com/iTBbX08hvjbc>

## 以ROOT登录：

右键win图标 以管理员运行windows终端 输入

```plaintext
kali config --default-user root
```

重启服务

```plaintext
net stop LxssManager
net start LxssManager
```

启动kali或命令行输入kali

修改密码

```plaintext
passwd root
```

## 配置：

1.安装vim

```plaintext
apt-get update
apt-get upgrade
apt-get install vim
```

2.换源

```plaintext
vim /etc/apt/sources.list
```

将原本的源地址注释，替换为以下源：

```plaintext
#中科大
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
 
#阿里云
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
 
#清华大学
deb http://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
 
#浙大
deb http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free
deb-src http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free
```

## 开启ssh

```plaintext
修改 sshd 配置文件：
vim /etc/ssh/sshd_config
去掉允许授权登陆前的注释；加上 PermitRootLogin yes。
 
开机自启动：
update-rc.d ssh enable
```

## 远程桌面：

1. 下载桌面

   ```plaintext
   apt-get install kali-desktop-xfce
   apt-get install xorg
   apt-get install xfce4
   apt-get install xrdp
   ```
2. 配置

   ```plaintext
   vim /etc/xrdp/xrdp.ini
   ```

   将两处端口改为3390

   ![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-27173450.png)
3. 重启服务

   ```plaintext
   service xrdp restart
   ```
4. 设置自启动

   ```plaintext
   update-rc.d xrdp enable
   ```
5. 连接

   win+r打开运行

   ```plaintext
   mstsc
   ```

   回车

   ```plaintext
   127.0.0.1:3390
   ```

   如不能进入 kali输入`ifconfig` 查看ip按这个ip进入

## 汉化：

1. 导入win10字体

   ```plaintext
   mkdir -p /usr/share/fonts/windows 
   cp -r /mnt/c/Windows/Fonts/*.ttf /usr/share/fonts/windows/
   ```
2. 安装字体管理器

   ```plaintext
   apt install -y fontconfig
   ```
3. 替换

   ```plaintext
   export LC_ALL=zh_CN.UTF8 and LANG=zh_CN.UTF8 and LANGUAGE=zh_CN.UTF8
   ```
4. 清理字体缓存`fc-cache`

## 汉化桌面：

```plaintext
vim /etc/profile
在文件中添加
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
```

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE2022-07-27173739.png)

重启xrdp服务

推荐文件夹使用英文目录

```plaintext
service xrdp start
```

## 安装中文输入法

```plaintext
apt install fcitx 
apt install fcitx-googlepinyin 
```

Linux使用ctrl+空格切换输入法

## 汉化火狐

1.右边三横-齿轮（preferences）

![%title插图%num](https://z3.ax1x.com/2021/10/31/ISuTw8.png)

2.搜索language->set***->select alanguage to add..->选择chinese简体->ok即可

3.重启浏览器

## 安装完整系统:

工具集约7G，完全体会占到40G左右

```plaintext
apt-get update 
apt-get install kali-linux-full
```

注意：新版本中`kali-linux-all`更名为`kali-linux-everything`

由于以上原因，报错请执行：

```plaintext
sudo apt-get install kali-linux-everything
```
