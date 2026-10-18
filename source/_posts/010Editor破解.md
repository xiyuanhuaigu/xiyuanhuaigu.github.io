---
title: "010Editor破解"
date: 2023-09-22 08:33:10
---
## 分析

010程序无壳可以放心使用，网上的破解方法也很多，但是大多数是使用注册机进行破解，这里我提供一个更简单的方法。

## 流程

首先下载32位的最新版的010 Editor，将其载入Xdbg，查看其汇编代码。

先按F9使程序运行起来，按shift+d搜索字符串，我们要搜索的内容为刚进入程序时弹出来的内容，Evaluation

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922084153.png)

猜测具有换行符的才是真正的进入界面弹的字符串，将所有的字符串加个断点（F2）

可以看到，符合试用版提示的字符串有4个（都是换行符结尾），这4条指令地址非常接近，双击其中一条指令查看汇编代码，发现它们都在同一个子程序里，只是判断语句的分支不同。按F9运行一下，果然在启动之前能够中断，说明这段子程序就是在程序启动时用于检查注册状态的。

找到该程序段的入口地址，对其流程图进行分析

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922084428.png)

使用右键有个流程图或者直接选中那行按G

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922084553.png)

可以看到出现一个分支语句cmp 它主要是将eax里面的内容与0xDB进行对比，若对比通过则跳转到右边的不通过则跳转到左边，选中cmp eax,DB这一行按G返回，找到注册标志后，改程序流向是无效的，必须找到设置注册标志的公共CALL，在CALL里边改程序流向，才能真正实现破解。在这个程序中，判断注册标志指令是cmp eax, 0xDB，它上面的CALL是一个模块外的系统子程序调用（调用的QT的相关代码），再往上没有CALL了，怎么办？这说明这个标志已经提前由真正的CALL算好了，下面的关键是怎么找到这个真正的CALL：

单击选中 cmp eax, 0xDB指令，按Shift+C，复制二进制指令码，然后再按Ctrl+Shift+B，即在当前模块中按“匹配特征”进行搜索，按Ctrl+V粘贴二进制指令码，在主程序中查找所有的cmp eax, 0xDB 指令，如下图所示。

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922085213.png)

点击确定

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922085302.png)

挨个点击跳转到程序块，发现每个cmp之前都有一个函数的调用语句，猜测这就是检查是否注册的函数。（反汇编后函数名字结果可能不同，自己仔细看）

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922085354.png)

选中该函数在反汇编中转到

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922085603.png)

然后再转一次，找到对应的程序块

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922085620.png)

转到之后如图所示

![image-20230922085657334](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20230922085657334.png)

可以看到有个JE指令这个是条件满足跳转指令，要实现破解直接将eax赋值为0XDB即可，同时让JE指令跳转到该语句即可。右键汇编改掉相关代码。

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922090003.png)

右键补丁将更改后的代码保存下来

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230922090110.png)

点击修补文件，保存相关名称即可

破击之后如下图所示（那个惜缘怀古是我自己加的）如果出现0 User License则证明破解成功。

![image-20230922090233478](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20230922090233478.png)
