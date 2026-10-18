---
title: "Prometheus+Grafana搭建和使用"
date: 2025-01-09 19:06:16
---
# Prometheus-Grafana搭建和使用

## 1.prometheus监控框架工具介绍

prometheus是由谷歌研发的一款开源的监控软件，它通过安装在远程机器上的exporter，通过HTTP协议从远程的机器收集数据并存储在本地的时序数据库上

同时Prometheus后端用 golang语言开发，前端是 Grafana

## 2.支持类型

Prometheus为了支持各种中间件以及第三方的监控提供了exporter，大家可以把它理解成监控适配器，将不同指标类型和格式的数据统一转化为Prometheus能够识别的指标类型。

例如Node exporter主要通过读取Linux的/proc以及/sys目录下的系统文件获取操作系统运行状态，reids exporter通过Reids命令行获取指标，mysql exporter通过读取数据库监控表获取MySQL的性能数据。他们将这些异构的数据转化为标准的Prometheus格式，并提供HTTP查询接口。

Prometheus的流行和Kubernetes密不可分，支持对Kubernetes、容器、OpenStack的监控。

## 3. Prometheus 源码安装和启动配置

| IP | 角色 | 系统 |
| --- | --- | --- |
| 192.168.3.64 | Prometheus 服务端 | Ubuntu20.4 |

普罗米修斯下载网址:<https://github.com/prometheus/prometheus/releases>

下载之后上传到服务器，

```plaintext
tar xf prometheus-3.1.0.linux-amd64.tar.gz
mv prometheus-3.1.0.linux-amd64 /prometheus
chamod 775 prometheus
```

查看版本

```plaintext
./prometheus --version

prometheus, version 3.1.0 (branch: HEAD, revision: 7086161a93b262aa0949dbf2aba15a5a7b13e0a3)
  build user:       root@74c225e2044f
  build date:       20250102-13:52:43
  go version:       go1.23.4
  platform:         linux/amd64
  tags:             netgo,builtinassets,stringlabels
```

### prometheus.yml 配置解释

```plaintext
# my global config
global:
  # 默认情况下，每15s拉取一次目标采样点数据。
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  # 每15秒评估一次规则。默认值为每1分钟。
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # job名称会增加到拉取到的所有采样点上，同时还有一个instance目标服务的host：port标签也会增加到采样点上
  - job_name: 'prometheus'

    # 覆盖global的采样点，拉取时间间隔5s
    scrape_interval: 5s
    static_configs:
    - targets: ['localhost:9090']
```

### 启动服务

```plaintext
# 启动服务
./prometheus --config.file=prometheus.yml

# 指定配置文件
--config.file="prometheus.yml"
# 默认指定监听地址端口，可修改端口
--web.listen-address="0.0.0.0:9090" 
# 最大连接数
--web.max-connections=512
# tsdb数据存储的目录，默认当前data/
--storage.tsdb.path="data/"
# premetheus 存储数据的时间，默认保存15天
--storage.tsdb.retention=15d 
# 通过命令热加载无需重启 curl -XPOST 192.168.3.64:9090/-/reload
--web.enable-lifecycle
# 可以启用 TLS 或 身份验证 的配置文件的路径
--web.config.file=""

启动选项了解：./prometheus --help
```

访问：[http://192.168.3.64:9090](http://192.168.3.64:9090/)

### 将Prometheus配置为系统服务

进入systemd目录下：cd /usr/lib/systemd/system

```plaintext
cd /usr/lib/systemd/system
```

创建文件：vim prometheus.service

```plaintext
[Unit]
  Description=https://prometheus.io
  
  [Service]
  Restart=on-failure
  ExecStart=/home/xiyuanhuaigu/prometheus/prometheus --config.file=/home/xiyuanhuaigu/prometheus/prometheus.yml  --web.listen-address=:9090

  [Install]                      
  WantedBy=multi-user.target
```

生效系统system文件

```plaintext
systemctl daemon-reload
```

启动服务

```plaintext
systemctl start prometheus
```

## 4.客户端安装node_exporter

因为只有一个主机，既是客户端又是服务端所以得安装

下载地址：<https://github.com/prometheus/node_exporter/releases>

```plaintext
tar xf node_exporter-1.8.2.linux-amd64.tar.gz
mv node_exporter-1.8.2.linux-amd64  node_exporter
```

添加启动

```plaintext
vim /usr/lib/systemd/system/node_exporter.service

[Unit]
Description=node_exporter
After=network.target 

[Service]
ExecStart=/home/xiyuanhuaigu/node_exporter/node_exporter
Restart=on-failure

[Install]
WantedBy=multi-user.target

# 启动node_exporter
systemctl daemon-reload
systemctl start node_exporter
```

## 5.Prometheus服务端配置文件添加监控项

```plaintext
[root@VM_2-45 /usr/local/prometheus]# cat prometheus.yml 
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['192.168.2.45:9090']

  - job_name: 'linux'
    static_configs:
    - targets: ['192.168.2.45:9100'] # 多个用,分开

# 添加上面三行
```

重启普罗米修斯

```plaintext
systemctl restart prometheus.service
```

## 6.安装grafana

安装必备软件包

```bash
sudo apt-get install -y apt-transport-https software-properties-common wget
```

导入 GPG 密钥

```bash
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
```

添加稳定版本的仓库，运行以下命令

```plaintext
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

更新可用软件包列表

```plaintext
sudo apt-get update
```

安装 Grafana OSS

```plaintext
sudo apt-get install grafana
```

启动

```plaintext
sudo systemctl daemon-reload
sudo systemctl start grafana-server
```

## 7.添加Prometheus数据源

选择数据源

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20250109222307.png)

导入IP

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20250109222347.png)

导入模板8919

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20250109222533.png)

选择添加

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20250109222612.png)
