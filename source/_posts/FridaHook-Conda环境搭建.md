---
title: "FridaHook Conda环境搭建"
date: 2024-06-12 23:14:44
---
因为安卓版本不一需要的frida环境也不一，所以直接用conda进行统一管理

# Anaconda 安装

直接下载windows版的，记得把环境变量也勾选上会方便很多

<https://www.anaconda.com/download/#windows>

然后安装就可以了

# Frida环境配置

这里主要讲windows上的环境搭建，需要在安卓端加入server才能正常运行，server端配置请看另一篇博客

<https://xiyuanhuaigu.github.io/2023/03/26/FridaHook%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/>

```plaintext
conda create -n androidRE python=3.8
```

然后在他系统自带的命令行中启动环境

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20240612232502.png)

```plaintext
conda activate androidRE

安装node环境

conda install -c conda-forge nodejs
```

之后进行测试

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20240612232622.png)

安装frida (适合安卓12)

```plaintext
pip install frida==16.2.2
```

```plaintext
pip install frida-tools==12.3.0
```

# VScode 配置Conda

按下Ctrl+Shift+P打开命令面板，输入“Python: Select Interpreter”并选择要使用的conda虚拟环境。

这样就可以愉快的使用了。
