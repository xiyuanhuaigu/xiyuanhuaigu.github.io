---
title: "Linux虚拟机固定ip"
date: 2023-04-11 23:01:22
---
# 设置静态IPV4

```plaintext
centos7的网络IP地址配置文件在/etc/sysconfig/network-scripts文件夹下。
查看当前网卡名称 ifconfig。
ens33网卡对应的配置文件为ifcfg-ens33，使用vim编辑。
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

修改前：

```plaintext
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="0bf3e1c3-1440-4e3a-afbe-94bb04004184"
DEVICE="ens33"
ONBOOT="yes"
```

修改后：

```plaintext
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"		# 使用静态IP地址，默认为dhcp
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPADDR="172.10.6.56"   	# 设置的静态IP地址
NETMASK="255.255.255.0"    	# 子网掩码
GATEWAY="172.10.6.254"    	# 网关地址(本地电脑打开cmd，执行ipconfig查询)
DNS1="8.8.8.8"       		# DNS服务器
DNS2="114.114.114.114"       	# DNS服务器
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="0bf3e1c3-1440-4e3a-afbe-94bb04004184"
DEVICE="ens33"
ONBOOT="yes"			# 是否开机启用
```

重启服务

```plaintext
service network restart

然后重新连接，执行ifconfig，如下图，IP已经变成设置的IP了。
```
