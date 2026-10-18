---
title: "使用kubeadm快速部署一个k8s集群"
date: 2023-01-13 17:59:36
---
kubeadm是官方社区推出的一个用于快速部署kubernetes集群的工具。

这个工具能通过两条指令完成一个kubernetes集群的部署：

```plaintext
# 创建一个 Master 节点
$ kubeadm init

# 将一个 Node 节点加入到当前集群中
$ kubeadm join <Master节点的IP和端口 >
```

## 1. 安装要求

在开始之前，部署Kubernetes集群机器需要满足以下几个条件：

- 一台或多台机器，操作系统 CentOS7.x-86_x64
- 硬件配置：2GB或更多RAM，2个CPU或更多CPU，硬盘30GB或更多
- 可以访问外网，需要拉取镜像，如果服务器不能上网，需要提前下载镜像并导入节点
- 禁止swap分区

## 2. 准备环境

| 角色 | IP |
| --- | --- |
| master | 192.168.203.132 |
| node1 | 192.168.203.133 |
| node2 | 192.168.203.131 |

```plaintext
# 关闭防火墙
systemctl stop firewalld
systemctl disable firewalld

# 关闭selinux
sed -i 's/enforcing/disabled/' /etc/selinux/config  # 永久
setenforce 0  # 临时

# 关闭swap
swapoff -a  # 临时
sed -ri 's/.*swap.*/#&/' /etc/fstab    # 永久

# 根据规划设置主机名
hostnamectl set-hostname <hostname>

# 在master添加hosts
cat >> /etc/hosts << EOF
192.168.203.132 k8s-master
192.168.203.133 k8s-node1
192.168.203.131 k8s-node2
EOF

# 将桥接的IPv4流量传递到iptables的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system  # 生效

# 时间同步
yum install ntpdate -y
ntpdate time.windows.com
```

## 3. 所有节点安装Docker/kubeadm/kubelet

Kubernetes默认CRI（容器运行时）为Docker，因此先安装Docker。

### 3.1 安装Docker

```plaintext
wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
yum -y install docker-ce
systemctl enable docker && systemctl start docker
```

配置镜像下载加速器：

```plaintext
vim /etc/docker/daemon.json

{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"],
  "exec-opts": ["native.cgroupdriver=systemd"]
}

systemctl restart docker
docker info                  #查看docker信息，进行确认
```

### 3.2添加阿里云软件源：

```plaintext
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

### 3.3 安装kubeadm，kubelet和kubectl

由于版本更新频繁，这里指定版本号部署：

```plaintext
$ yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
$ systemctl enable kubelet
```

## 4. 部署Kubernetes Master

在192.168.203.132（Master）执行。

```plaintext
$ kubeadm init --apiserver-advertise-address=192.168.203.132 --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.18.0 --service-cidr=10.96.0.0/12 --pod-network-cidr=10.244.0.0/16
```

由于默认拉取镜像地址k8s.gcr.io国内无法访问，这里指定阿里云镜像仓库地址。

- -apiserver-advertise-address 集群通告地址 (与master的Ip保持一致)
- –image-repository 由于默认拉取镜像地址k8s.gcr.io国内无法访问，这里指定阿里云镜像仓库地址
- –kubernetes-version K8s版本，与上面安装的一致
- –service-cidr 集群内部虚拟网络，Pod统一访问入口
- –pod-network-cidr Pod网络，与下面部署的CNI网络组件yaml中保持一致

初始化之后，会输出一个join命令，先复制出来，node节点加入master会使用。

使用kubectl工具：

```plaintext
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

查看工作节点

```plaintext
kubectl get nodes
```

## 5.加入Kubernetes Node

在192.168.203.133/131（Node）执行。

向集群添加新节点，执行在kubeadm init输出的kubeadm join命令：

```plaintext
kubeadm join 192.168.203.132:6443 --token esce21.q6hetwm8si29qxwn \
   --discovery-token-ca-cert-hash sha256:00603a05805807501d7181c3d60b478788408cfe6cedefedb1f97569708be9c5
```

默认token有效期为24小时，当过期之后，该token就不可用了。这时就需要重新创建token，操作如下：

```plaintext
kubeadm token create --print-join-command
```

## 6.部署容器网络 （master执行）

Calico是一个纯三层的数据中心网络方案，是目前Kubernetes主流的网络方案。

下载YAML：

```plaintext
wget https://docs.projectcalico.org/v3.14/manifests/calico.yaml
```

下载完后还需要修改里面定义Pod网络（CALICO_IPV4POOL_CIDR），与前面kubeadm init的 –pod-network-cidr指定的一样。

修改完后文件后，进行部署：

```
kubectl apply -f calico.yaml
kubectl get pods -n kube-system                        #执行结束要等上一会才全部running
```
