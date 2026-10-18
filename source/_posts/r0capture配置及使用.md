---
title: "r0capture配置及使用"
date: 2023-08-15 21:59:35
---
# r0capture配置及使用

## 下载

github地址：<https://github.com/r0ysue/r0capture>

## 配置

### 安装依赖

```plaintext
Python版本>=3.6
pip install loguru
pip install click
```

### 安装 frida

详见另一篇博客

## 使用

先启动frida

```plaintext
cd /data/local/tmp
./fsarm64
```

切记仅限安卓平台7、8、9、10、11 可用 ，禁止使用模拟器。

- Spawn 模式：

```plaintext
$ python3 r0capture.py -U -f com.coolapk.market -v
```

- Attach 模式，抓包内容保存成pcap文件供后续分析：

```plaintext
$ python3 r0capture.py -U 酷安 -v -p iqiyi.pcap
```

建议使用`Attach`模式，从感兴趣的地方开始抓包，并且保存成`pcap`文件，供后续使用Wireshark进行分析。

> 老版本Frida使用包名，新版本Frida使用APP名。APP名必须是点开app后，frida-ps -U显示的那个app名字。

- 收发包函数定位：`Spawn`和`attach`模式均默认开启；

> 可以使用`python r0capture.py -U -f cn.soulapp.android -v >> soul3.txt`这样的命令将输出重定向至txt文件中稍后过滤内容

- 客户端证书导出功能：默认开启；必须以Spawm模式运行；

> 运行脚本之前必须手动给App加上存储卡读写权限；

> 并不是所有App都部署了服务器验证客户端的机制，只有配置了的才会在Apk中包含客户端证书

> 导出后的证书位于/sdcard/Download/包名xxx.p12路径，导出多次，每一份均可用，密码默认为：r0ysue，推荐使用[keystore-explorer](http://keystore-explorer.org/)打开查看证书。

- 新增host连接方式“-H”，用于Frida-server监听在非标准端口时的连接。有些App会检测Frida标准端口，因此frida-server开在非标准端口可以绕过检测。
