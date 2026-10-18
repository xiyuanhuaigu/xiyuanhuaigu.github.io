---
title: "FridaHook环境搭建"
date: 2023-03-26 19:21:18
---
frida版本：14.2.18

frida-tools：9.2.5

python：3.7

此配置适用于Android10版本的逆向，具体版本适配请参考官网

# 一.安装python3.7

前往python[官网](https://www.python.org/downloads/windows/)找到3.7版本下载，网上方法很多这里不在赘述

# 二.安装 Frida和 Frida-tools

```plaintext
pip install frida==14.2.18
pip install frida-tools==9.2.5
```

若没科学上网环境可能很慢，等个30分钟左右就差不多了

检查环境：

```plaintext
frida --version
```

若出现14.2.18则Frida-tools安装成功

进入python解释器

```plaintext
import  frida
```

若不报错则 frida安装成功
