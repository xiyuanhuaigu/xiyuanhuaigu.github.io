---
title: "whatweb使用教程"
date: 2022-07-30 20:36:04
---
# 常规扫描：

```plaintext
whatweb 域名
```

# 批量扫描：

我们可以通过将很多要扫描的域名放在文件内，然后扫描时指定该文件即可。

```plaintext
whatweb -i /root/target.txt
```

# 详细回显扫描：

```plaintext
whatweb -v 域名
```

# 强度扫描等级控制：

whatweb有一个-aggression（简写为-a）参数，此参数后边可以跟数字1-4，分别对应4个不同的等级。

1. stealthy 每个目标发送一次http请求，并且会跟随重定向
2. unused 不可用（从2011年开始，此参数就是在开发状态
3. aggressive 每个目标发送少量的http请求，这些请求时根据参数为1时结果确定的
4. heavy 每个目标会发送大量的http请求，会去尝试每一个插件

whatweb -a 等级 域名 （可以和-v参数结合使用

# 快速本地扫描（扫描内网主机）：

```plaintext
whatweb --no-errors -t 255 内网网段 （可以和-a和-v参数结合使用
```

# 将扫描结果导出至文件内：

```plaintext
whatweb http://www.baidu.com --log-xml=baidu.xml
```

```plaintext
--log-brief=FILE            简单的记录，每个网站只记录一条返回信息
--log-verbose=FILE            详细输出
--log-xml=FILE            返回xml格式的日志
--log-json=FILE            以json格式记录日志
--log-json-verbose=FILE            记录详细的json日志
--log-magictree=FILE            xml的树形结构
--log-object=FILE            ruby对象格式
--log-mongo-database            mongo数据库格式
```

注：json格式需要安装json依赖sudo gem install json

Mongo格式需要安装mongo依赖sudo gem install json
