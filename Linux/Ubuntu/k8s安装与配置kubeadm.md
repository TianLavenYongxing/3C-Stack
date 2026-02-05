
## 配置主机 

| 角色      | 主机名        | IP           |
| ------- | ---------- | ------------ |
| master  | k8s-master | 192.168.1.10 |
| worker1 | k8s-node1  | 192.168.1.11 |
| worker2 | k8s-node2  | 192.168.1.12 |
### 设置主机名
1. 临时 + 永久修改
```bash
sudo hostnamectl set-hostname k8s-master
sudo hostnamectl set-hostname k8s-node1
sudo hostnamectl set-hostname k8s-node2
```
2. 立即生效
```bash
exec bash
```
3. 验证
```bash
hostname
hostnamectl
```
### 编辑 `/etc/hosts`
```bash
sudo vim /etc/hosts
```
追加
```text
192.168.1.10  k8s-master
192.168.1.11  k8s-node1
192.168.1.12  k8s-node2
```
### 关闭 swap
```bash
swapoff -a
sed -i '/ swap / s/^/#/' /etc/fstab
```
### 时间同步
```bash
timedatectl status
```
### 内核参数
```bash
lsmod | grep br_netfilter
```

## 安装与配置 Containerd

## 安装与配置Crictl

## 安装 kubeadm / kubelet / kubectl
1. 安装基础工具
```bash
apt update
apt install -y apt-transport-https ca-certificates curl gpg
```
2. 添加 Kubernetes 官方 GPG key
```bash
mkdir -p /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key \
| gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
3. 添加 Kubernetes 软件源
```bash
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /" \
| tee /etc/apt/sources.list.d/kubernetes.list
```
4. 关键验证（非常重要）
```bash
apt update
```
**必须看到类似这一行** ：
```text
Get: x https://pkgs.k8s.io/core:/stable:/v1.29/deb InRelease
```
### 安装 kubeadm / kubelet / kubectl