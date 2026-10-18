---
title: "k8s公网部署"
date: 2023-03-06 16:49:16
---
```plaintext
CentOS 7.6 系统主机 3 台，基于公网 IP 搭建 1 主 2 从的 Kubernetes 集群 
```

# 一、准备工作

## 1.版本信息

| Docker | 20.10.21 |
| --- | --- |
| Kubernetes | 1.21.0-0 |
| Flannel | 0.20.2 |

## 2.集群角色规划

3 台 CentOS 7.6 主机，集群角色规划如下

|  | **Master** | **Worker01** | **Worker02** |
| --- | --- | --- | --- |
| **公网 IP** | 139.196.219.92 | 1.116.156.102 | 121.37.169.103 |
| **内网 IP** | 172.21.253.164 | 10.0.4.15 | 192.168.0.89 |
| **服务器厂商** | 阿里云 | 腾讯云 | 阿里云云 |

## 3.修改 hosts 文件

设置 master 主机的 hostname 为 m

```arduino
sudo hostnamectl set-hostname m
```

分别设置 2 台 worker 主机的 hostname

```arduino
sudo hostnamectl set-hostname w1 
sudo hostnamectl set-hostname w2
```

分别修改 3 台主机的 hosts 文件，这里配置的是公网 IP。因为云服务器厂商不同，无法搭建局域网 K8s 集群

```bash
vim /etc/hosts
 
139.196.219.92 m 
1.116.156.102 w1 
121.37.169.103 w2
```

## 4.创建虚拟网卡

3 台主机分别填写对应的公网 IP

```ini
# 打开文件
vim /etc/sysconfig/network-scripts/ifcfg-eth0:1

# 填入内容
NAME=eth0:1
DEVICE=eth0:1
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=static
NETMASK=255.255.255.0
IPADDR=<public ip>

# 重启网络
systemctl restart network.service
```

注：CentOS 8取消了network.service，若命令不成功请自行百度

创建虚拟网卡前，查看 eth0

```csharp
[root@w1 network-scripts]# ip a | grep eth0

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    inet 10.0.4.15/22 brd 10.0.7.255 scope global eth0
```

创建虚拟网卡后，查看 eth0，可以发现多了一条记录 eth0:1

```sql
[root@w1 ~]# ip a | grep eth0

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    inet 10.0.4.15/22 brd 10.0.7.255 scope global eth0
    inet 1.116.156.102/24 brd 1.116.156.255 scope global eth0:1
```

## 5.云服务器安全组设置

搭建 K8s 集群需要对云服务器安全组入方向规则进行配置，开启相应的端口

master 节点

| **协议** | **端口** | **作用** | **使用者** |
| --- | --- | --- | --- |
| TCP | 2379～2380 | etcd 客户端 API | kube-apiserver, etcd |
| TCP | 6443 | api-server API | 所有组件 |
| UDP | 8472 | VxLan Overlay 网络通信 | Flannel 网络插件 |
| TCP | 10250 | kubelet API | kubelet, Control Plane 组件 |
| TCP | 10251 | kube-scheduler | kube-scheduler |
| TCP | 10252 | kube-controller-manager | kube-controller-manager |

worker 节点

| **协议** | **端口** | **作用** | **使用者** |
| --- | --- | --- | --- |
| UDP | 8472 | VxLan Overlay 网络通信 | Flannel 网络插件 |
| TCP | 10250 | kubelet API | kubelet, Control Plane 组件 |
| TCP | 30000~32767 | NodePort 服务 | 所有组件 |

# 二、系统基础配置

## 1.更新并安装依赖

准备好 3 台主机后，每台主机均需要更新并安装依赖

```sql
sudo yum -y update 
sudo yum install -y conntrack ipvsadm ipset jq sysstat curl iptables libseccomp
sudo yum install -y yum-utils
```

## 2.基础配置

关闭防火墙

```arduino
systemctl stop firewalld && systemctl disable firewalld
```

关闭 SELinux (Security Enhanced Linux)

```arduino
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```

关闭 swap

```bash
swapoff -a
sed -i '/swap/s/^(.*)$/#\1/g' /etc/fstab
```

配置 iptables 的 ACCEPT 规则

```r
iptables -F && iptables -X && iptables -F -t nat && iptables -X -t nat && iptables -P FORWARD ACCEPT
```

设置系统参数

```bash
cat <<EOF> /etc/sysctl.d/k8s.conf 
net.bridge.bridge-nf-call-ip6tables = 1 
net.bridge.bridge-nf-call-iptables = 1 
EOF
sysctl --system
```

# 三、安装 Docker

## 1.配置阿里云镜像源

```csharp
sudo yum-config-manager \
    --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 查看 Docker-CE
yum list | grep docker-ce

# 更新 yum 缓存
sudo yum makecache fast
```

## 2.安装 Docker

安装指定版本 20.10.21

```lua
sudo yum install -y docker-ce-20.10.21 docker-ce-cli-20.10.21 containerd.io
```

## 3.启动 Docker

```bash
# 启动 Docker
sudo systemctl start docker
# 设置开机启动 Docker
sudo systemctl enable docker
```

# 四、安装 Kubernetes 集群所需组件

## (一) 安装 kubeadm, kubelete, kubectl

### 1.配置 yum 源

```ini
cat <<EOF> /etc/yum.repos.d/kubernetes.repo 
[kubernetes] 
name=Kubernetes 
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64 
enabled=1 
gpgcheck=0 
repo_gpgcheck=0 
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg 
    http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg 
EOF
```

### 2.开始安装

```bash
# 确认 yum 中是否有指定版本
yum list kubeadm --showduplicates | sort -r

# 安装 1.21.0-0
yum install -y kubeadm-1.21.0-0 kubelet-1.21.0-0 kubectl-1.21.0-0
```

### 3.Docker 和 K8s 设置为同一个 cgroup

```bash
(1) 修改 daemon.json
vim /etc/docker/daemon.json
设置 cgroup
"exec-opts": ["native.cgroupdriver=systemd"]

(2) 重启 Docker
systemctl restart docker

(3) 检查 kubelet，如果在输出信息中发现 No such file or directory，说明没问题
sed -i "s/cgroup-driver=systemd/cgroup-driver=cgroupfs/g" /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```

### 4.修改 kubelet 启动参数

每台主机都要添加并指定对应的公网 IP，然后才能使用公网 IP 进行集群间通信

```bash
vim /usr/lib/systemd/system/kubelet.service.d/10-kubeadm.conf
```

在 KUBELET_KUBECONFIG_ARGS 后面追加 –node-ip=（此处填写你的公网ip）

修改之后执行 daemon-reload 让修改生效

```plaintext
systemctl daemon-reload
```

### 5.启动 kubelet / 重启 kubelet

启动 kubelet

```bash
systemctl enable kubelet && systemctl start kubelet
```

重启 kubelet

```plaintext
systemctl restart kubelet
```

## (二) 拉取 kube-proxy, scheduler 等镜像

需要通过国内镜像源下载镜像

### 1.查看 kubeadm 所需镜像

```plaintext
kubeadm config images list
```

输出信息如下

```bash
k8s.gcr.io/kube-apiserver:v1.21.14
k8s.gcr.io/kube-controller-manager:v1.21.14
k8s.gcr.io/kube-scheduler:v1.21.14
k8s.gcr.io/kube-proxy:v1.21.14
k8s.gcr.io/pause:3.4.1
k8s.gcr.io/etcd:3.4.13-0
k8s.gcr.io/coredns/coredns:v1.8.0
```

遗憾的是需要科学上网才能下载这些镜像

### 2.尝试用国内镜像源拉取镜像

```bash
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.21.14
```

经过测试，可以正常拉取，因此编写一个 Shell 脚本，通过国内镜像源拉取 kubeadm 所需镜像

切换到一个目录，编写 kubeadm_image.sh，用于从阿里云镜像源拉取镜像 / 重新打 tag / 删除原镜像

```bash
#!/usr/bin/env bash

# 镜像处理过程中，如果遇到错误，立即退出
set -e

# 版本定义
readonly KUBE_VERSION=v1.21.0
readonly PAUSE_VERSION=3.4.1
readonly ETCD_VERSION=3.4.13-0
readonly CORE_DNS_VERSION=v1.8.0
readonly OFFICIAL_URL=k8s.gcr.io
readonly ALIYUN_URL=registry.cn-hangzhou.aliyuncs.com/google_containers

# 镜像列表
imageList=(kube-apiserver:${KUBE_VERSION} 
kube-controller-manager:${KUBE_VERSION} 
kube-scheduler:${KUBE_VERSION} 
kube-proxy:${KUBE_VERSION} 
pause:${PAUSE_VERSION} 
etcd:${ETCD_VERSION} 
coredns:${CORE_DNS_VERSION})

# 镜像转换操作
for imageItem in ${imageList[@]} ; do 
  # 从国内镜像源拉取镜像
  docker pull $ALIYUN_URL/$imageItem
  # 给镜像重新打一个标签，命名为 kubeadm 所需的镜像
  docker tag $ALIYUN_URL/$imageItem $OFFICIAL_URL/$imageItem
  # 删除原有镜像
  docker rmi $ALIYUN_URL/$imageItem
done

# coredns 的镜像比较特殊，单独处理
docker tag ${OFFICIAL_URL}/coredns:${CORE_DNS_VERSION} ${OFFICIAL_URL}/coredns/coredns:${CORE_DNS_VERSION}
docker rmi ${OFFICIAL_URL}/coredns:${CORE_DNS_VERSION
```

运行脚本

```bash
sh ./kubeadm_image.sh
```

# 五、搭建 Kubernetes 集群

## (一)用 kubeadm 初始化 master 节点

### 1.执行 kubeadm init

```plaintext
kubeadm init --kubernetes-version=1.21.0 \
  --apiserver-advertise-address=39.98.182.32 \
  --pod-network-cidr=10.244.0.0/16 \
  -v=5
```

输出日志中出现如下信息时，说明 master 节点已经初始化成功了

> Your Kubernetes control-plane has initialized successfully!

将末尾的 kubeadm join 信息保存起来，后面的步骤中需要在 worker 节点执行

### 2.集群健康检查

执行如下命令

```plaintext
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

集群健康检查

```plaintext
# 检查集群状态
kubectl cluster-info

# 健康检查
curl -k https://localhost:6443/healthz
```

### 3.修改kube-apiserver 配置

kube-apiserver 添加 –bind-address=0.0.0.0，确认 –advertise-addres=<公网 IP>

```bash
vim /etc/kubernetes/manifests/kube-apiserver.yaml
```

## (二)安装网络插件 Flannel

### 1.当前集群状态

```sql
kubectl get pods -n kube-system
kubectl get nodes
```

可以看到，两个 coredns 还是 Pending 状态，此时还缺少网络插件

### 2.安装 Flannel 网络插件

Kubernetes 为了让网络功能更加灵活，制定了 CNI 规范，由第三方实现网络的细节功能。目前有多种网络插件可供选择，使用较多的是 Calico 和 Flannel，其他的网络插件参考官方文档：[链接](https://link.juejin.cn/?target=https://kubernetes.io/docs/concepts/cluster-administration/addons/)

由于公网环境使用 Calico 网络插件配置比较复杂，也没有调试成功，因此本文使用 Flannel 网络插件，安装命令如下，在 master 节点执行

```ruby
# 进入一个目录，假设为 /var/local/k8s
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

修改 kube-flannel.yml，新增 2 个配置

```plaintext
vim kube-flannel.yml
```

第 1 处

```plaintext
containers:
- name: kube-flannel
  #image: flannelcni/flannel:v0.20.2 #for ppc64le and mips64le (dockerhub limitations may apply)
  image: docker.io/rancher/mirrored-flannelcni-flannel:v0.20.2
  command:
  - /opt/bin/flanneld
  args:
  - --public-ip=$(PUBLIC_IP)
  - --iface=eth0
  - --ip-masq
  - --kube-subnet-mgr
```

第 2 处

```plaintext
env:
- name: PUBLIC_IP
  valueFrom:
    fieldRef:
      fieldPath: status.podIP
```

执行安装命令

```plaintext
kubectl apply -f kube-flannel.yml
```

## (三)用 kubeadm 将从节点加入集群

执行如下命令，将 worker 节点加入集群

```sql
kubeadm join 139.196.219.92:6443 --token o0zxc6.fmmh2sn8wlbt9onm \
  --discovery-token-ca-cert-hash sha256:a52cec58178c402ecaecc74375d49495c1bf13661dd0b25b158e7caf5d619fa6 \
  -v=5
复制代码
```

输出日志中出现如下信息时，说明 worker 节点已经成功加入集群

> This node has joined the cluster

# 六、部署dashboard

## 1、部署

kubernetes官方提供的可视化界面

<https://github.com/kubernetes/dashboard>

```plaintext
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml
```

```plaintext
# Copyright 2017 The Kubernetes Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

apiVersion: v1
kind: Namespace
metadata:
  name: kubernetes-dashboard

---

apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard

---

kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
spec:
  ports:
    - port: 443
      targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard

---

apiVersion: v1
kind: Secret
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-certs
  namespace: kubernetes-dashboard
type: Opaque

---

apiVersion: v1
kind: Secret
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-csrf
  namespace: kubernetes-dashboard
type: Opaque
data:
  csrf: ""

---

apiVersion: v1
kind: Secret
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-key-holder
  namespace: kubernetes-dashboard
type: Opaque

---

kind: ConfigMap
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard-settings
  namespace: kubernetes-dashboard

---

kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
rules:
  # Allow Dashboard to get, update and delete Dashboard exclusive secrets.
  - apiGroups: [""]
    resources: ["secrets"]
    resourceNames: ["kubernetes-dashboard-key-holder", "kubernetes-dashboard-certs", "kubernetes-dashboard-csrf"]
    verbs: ["get", "update", "delete"]
    # Allow Dashboard to get and update 'kubernetes-dashboard-settings' config map.
  - apiGroups: [""]
    resources: ["configmaps"]
    resourceNames: ["kubernetes-dashboard-settings"]
    verbs: ["get", "update"]
    # Allow Dashboard to get metrics.
  - apiGroups: [""]
    resources: ["services"]
    resourceNames: ["heapster", "dashboard-metrics-scraper"]
    verbs: ["proxy"]
  - apiGroups: [""]
    resources: ["services/proxy"]
    resourceNames: ["heapster", "http:heapster:", "https:heapster:", "dashboard-metrics-scraper", "http:dashboard-metrics-scraper"]
    verbs: ["get"]

---

kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
rules:
  # Allow Metrics Scraper to get metrics from the Metrics server
  - apiGroups: ["metrics.k8s.io"]
    resources: ["pods", "nodes"]
    verbs: ["get", "list", "watch"]

---

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: kubernetes-dashboard
subjects:
  - kind: ServiceAccount
    name: kubernetes-dashboard
    namespace: kubernetes-dashboard

---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: kubernetes-dashboard
subjects:
  - kind: ServiceAccount
    name: kubernetes-dashboard
    namespace: kubernetes-dashboard

---

kind: Deployment
apiVersion: apps/v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: kubernetes-dashboard
  template:
    metadata:
      labels:
        k8s-app: kubernetes-dashboard
    spec:
      containers:
        - name: kubernetes-dashboard
          image: kubernetesui/dashboard:v2.3.1
          imagePullPolicy: Always
          ports:
            - containerPort: 8443
              protocol: TCP
          args:
            - --auto-generate-certificates
            - --namespace=kubernetes-dashboard
            # Uncomment the following line to manually specify Kubernetes API server Host
            # If not specified, Dashboard will attempt to auto discover the API server and connect
            # to it. Uncomment only if the default does not work.
            # - --apiserver-host=http://my-address:port
          volumeMounts:
            - name: kubernetes-dashboard-certs
              mountPath: /certs
              # Create on-disk volume to store exec logs
            - mountPath: /tmp
              name: tmp-volume
          livenessProbe:
            httpGet:
              scheme: HTTPS
              path: /
              port: 8443
            initialDelaySeconds: 30
            timeoutSeconds: 30
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            runAsUser: 1001
            runAsGroup: 2001
      volumes:
        - name: kubernetes-dashboard-certs
          secret:
            secretName: kubernetes-dashboard-certs
        - name: tmp-volume
          emptyDir: {}
      serviceAccountName: kubernetes-dashboard
      nodeSelector:
        "kubernetes.io/os": linux
      # Comment the following tolerations if Dashboard must not be deployed on master
      tolerations:
        - key: node-role.kubernetes.io/master
          effect: NoSchedule

---

kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: dashboard-metrics-scraper
  name: dashboard-metrics-scraper
  namespace: kubernetes-dashboard
spec:
  ports:
    - port: 8000
      targetPort: 8000
  selector:
    k8s-app: dashboard-metrics-scraper

---

kind: Deployment
apiVersion: apps/v1
metadata:
  labels:
    k8s-app: dashboard-metrics-scraper
  name: dashboard-metrics-scraper
  namespace: kubernetes-dashboard
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: dashboard-metrics-scraper
  template:
    metadata:
      labels:
        k8s-app: dashboard-metrics-scraper
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: 'runtime/default'
    spec:
      containers:
        - name: dashboard-metrics-scraper
          image: kubernetesui/metrics-scraper:v1.0.6
          ports:
            - containerPort: 8000
              protocol: TCP
          livenessProbe:
            httpGet:
              scheme: HTTP
              path: /
              port: 8000
            initialDelaySeconds: 30
            timeoutSeconds: 30
          volumeMounts:
          - mountPath: /tmp
            name: tmp-volume
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            runAsUser: 1001
            runAsGroup: 2001
      serviceAccountName: kubernetes-dashboard
      nodeSelector:
        "kubernetes.io/os": linux
      # Comment the following tolerations if Dashboard must not be deployed on master
      tolerations:
        - key: node-role.kubernetes.io/master
          effect: NoSchedule
      volumes:
        - name: tmp-volume
          emptyDir: {}
```

## 2、设置访问端口

```plaintext
kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
```

type: ClusterIP 改为 type: NodePort

```plaintext
kubectl get svc -A |grep kubernetes-dashboard
## 找到端口，在安全组放行
```

```plaintext
访问： https://集群任意IP:端口      https://139.198.165.238:32759
```

## 3、创建访问账号

```plaintext
#创建访问账号，准备一个yaml文件； vi dash.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

```plaintext
kubectl apply -f dash.yaml
```

## 4、令牌访问

```plaintext
#获取访问令牌
kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
```

根据令牌登录即可

# 七、常见错误解决

```plaintext
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

原因：kubernetes master没有与本机绑定，集群初始化的时候没有绑定，此时设置在本机的环境变量即可解决问题。

解决：

```plaintext
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> /etc/profile
```

使生效

```plaintext
source /etc/profile
```
