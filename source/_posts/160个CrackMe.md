---
title: "160个CrackMe"
date: 2023-04-03 16:13:53
---
# CrackMe001

程序没有加壳，可以放心逆向

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230403162340.png)

拖入OD找到以下字符串，可以看到下面的Failed！是错误的弹出，上面的Congratz！是正确的弹出。

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230403162501.png)

此处调用了比较的函数，在此函数处下一个断点，EAX处储存着输入的字符，EDX处储存着需要比较的字符，由于字符是直接写死，所以第一个序列号是一个固定的硬编 码 Hello Dude!

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230404172309.png)

输入 Hello Dude! 程序弹出正确提示 序列号部分完成

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230404173059.png)

接下来分析用户名和序列号的部分 这个部分比单纯的序列号要有点难度，先找到入口函数。

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230404203323.png)

首先程序会取输入的用户名的第一位，后将第一位乘0x29再乘2，修改完后的数据存在原来的内存地址中

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230404203544.png)

后通过栈中的操作，在字符串的前后分别添加  `CW-`与`-CRACKED`最后将输入的密码与拼接好的字符串进行匹配。

注册机：

```python
str1 = 'CW-'
str2 = '-CRACKED'
username = input("请输入用户名:")
username = username[:1:]
username = str(ord(username) * int('0x29', 16) * 2)
print("密码:"+str1+username+str2)
```

# CrackMe002

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230422195820.png)

程序提示无壳

随便输入一个，他提示You Get Wrong,直接去超级字符串+中搜索，发现相应的代码，也可以看到跳转的代码

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230423194306.png)

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230423204409.png)

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230423204726.png)

该程序首先计算用户名的长度，然后获取用户名的首字母的ascii码值，然后将长度*0x17CFB+用户名首字母的ascii值并将得到的16进制转10进制，最后与密码进行比较。

注册机：

```python
str1 = input("请输入账户：")
len = len(str1)
print("AKA-"+str(len * 0x17CFB + ord(str1[0])))
```

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230423204327.png)
