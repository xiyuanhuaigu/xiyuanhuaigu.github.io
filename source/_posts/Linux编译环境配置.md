---
title: "Linux编译环境配置"
date: 2022-07-01 10:30:08
---
# Linux下的编译环境配置

## C/C++环境配置

### C编译环境的配置

```plaintext
apt-get install gcc
```

执行结束后我们可以查看版本

```plaintext
gcc --version
```

#### 使用

```plaintext
gcc --h
```

可以查看该命令的所有操作

接下来我们写一个HelloWorld.c并编译运行。

vim HelloWorld.c

#include<stdio.h>  
int main(void)

{  
printf(“Hello World!\n”);  
}

保存好后进行编译

gcc HelloWorld.c -o HelloWolrd

编译成功后运行

./HelloWorld

### C++编译环境配置

```plaintext
apt-get install g++
```

查看版本

```plaintext
g++ --version
```

使用

```plaintext
g++ --h
```

查看该命令的所有操作

然后接下来我们用C++写一个HelloWorld.cpp

vim HelloWorld.cpp

#include  
using namespace std;  
int main(void){  
cout<<”Hello World!”<<endl;  
}

g++ HelloWorld.cpp -o HelloWorldcpp

./HelloWorldcpp

## JAVA环境配置

输入`apt install openjdk-8-jre-headless`

成功安装
