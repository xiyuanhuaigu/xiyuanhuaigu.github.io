---
title: "BurpSuite 的安装"
date: 2022-01-08 19:37:34
---
以下内容仅供交流学习，支持正版  
参考链接<https://www.sqlsec.com/2020/10/winbp.html>  
 <https://blog.csdn.net/dantesite/article/details/107916493>

## 一：

由于最新的BurpSuite需要高版本的JAVA所以我们先配置自己的JAVA环境。  
这里不再过多赘述，网上都有

## 二：

Burp Suite Professional 2020.11.3  
平台 下载链接  
JAR <https://portswigger.net/burp/releases/download?product=pro&version=2020.11.3&type=Jar>  
Linux (64-bit) <https://portswigger.net/burp/releases/download?product=pro&version=2020.11.3&type=Linux>  
Mac OSX <https://portswigger.net/burp/releases/download?product=pro&version=2020.11.3&type=MacOsx>  
Windows (64-bit) <https://portswigger.net/burp/releases/download?product=pro&version=2020.11.3&type=WindowsX64>  
Burp Suite Enterprise Edition 2020.11  
平台 下载链接  
Linux (64-bit) <https://portswigger.net/burp/releases/download?product=enterprise&version=2020.11&type=Linux>  
Mac OSX <https://portswigger.net/burp/releases/download?product=enterprise&version=2020.11&type=MacOsx>  
Windows (64-bit) <https://portswigger.net/burp/releases/download?product=enterprise&version=2020.11&type=WindowsX64>  
使用之前版本的注册机依然是可以注册成功的，模仿一下之前版本的激活方式进行激活  
链接：<https://pan.baidu.com/s/16Ujp09AOqT0oN_SJEh0b0w>  
提取码：eld1  
密码：1564k  
将五个文件放在同一级目录下  
1.下载两个文件到同一级目录  
2.解压 Burp Suite Professional 2020.9.2.7z.001  
注：必须是英文路径。

## 三：

解压后找到注册机.vbs。

![](/2022/01/08/BurpSuite/2.png)

将License Text改为任意值，复制License。  
点击run

![](/2022/01/08/BurpSuite/3.png)

复制上一步生成的该序列号到Burp licence的输入框中。然后点击next进入下一步激活流程。

![](/2022/01/08/BurpSuite/4.png)

由于序列号不是官方购买，因此我们需要点击Manual activation，手动离线激活Burp。

![](/2022/01/08/BurpSuite/5.png)

点击右侧Copy request，将离线激活请求码复制到激活软件的Activation Request输入框中，在Activation Response中将会自动生成离线激活请求响应码，将该响应码复制到Brup离线激活界面的最后一个输入框中，然后点击next完成激活。

![](/2022/01/08/BurpSuite/6.png)

## 四：

给 Burp Suite Professional CN.vbs文件在桌面上创建一个快捷方式：

![](/2022/01/08/BurpSuite/7.png)

然后直接 「右键」选择「更改图标」找到 BP 根目录下的 ico 图标应用一下即可
