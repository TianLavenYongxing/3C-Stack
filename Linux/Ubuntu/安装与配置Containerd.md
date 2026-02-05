### 安装依赖
```bash
sudo apt upgrade
或者
sudo apt upgrade -y
```
### 安装containerd
```bash
sudo apt install -y containerd
```
**验证**
```bash
containerd --version
```
### 初始化 containerd 配置（**关键步骤**）
1. 生成默认配置文件
```bash
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
```
2. 设置 systemd cgroup（K8s 必做）
```bash
sudo vi /etc/containerd/config.toml
```
修改
```text
SystemdCgroup = true
```
重启并设置开机启动
```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

### 配置 crictl（可选，但是建议安装）
```bash
VERSION="v1.35.0"
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/${VERSION}/crictl-${VERSION}-linux-amd64.tar.gz

tar -zxvf crictl-${VERSION}-linux-amd64.tar.gz
sudo mv crictl /usr/local/bin/

crictl version
```
> `crictl` 是 **Kubernetes 容器运行时接口（CRI）** 的一个 **命令行调试工具**，主要用于 **直接与容器运行时（如 containerd、CRI-O）交互**，而**不依赖 kubelet 或 kubectl**。
1. 创建 crictl 配置
```bash
sudo vi /etc/crictl.yaml
```
内容：
```yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 10
debug: false
```
2. 验证 CRI
```
crictl info
```
正常会返回 containerd 的 runtime 信息。
更换国内华为云 SWR 镜像加速
```bash
vi /etc/containerd/config.toml
```
配置 **docker.io 的 mirror**：
```text
位置:[plugins."io.containerd.grpc.v1.cri".registry.mirrors]下方插入
[plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]
  endpoint = [
    "https://registry.cn-hangzhou.aliyuncs.com",
    "https://docker.mirrors.ustc.edu.cn",
    "http://hub-mirror.c.163.com",
    "https://docker.1panel.live",
    "https://hub.littlediary.cn",
    "https://docker.kejilion.pro",
    "https://docker.1ms.run",
    "https://lispy.org",
    "https://docker.xiaogenban1993.com",
    "https://docker.xuanyuan.me",
    "https://docker.mybacc.com",
    "https://docker-0.unsee.tech",
    "https://dockerpull.cn"
  ]
```
### 基础验证
查看服务状态
```bash
systemctl status containerd
```
拉取镜像测试
```bash
crictl pull nginx:latest
```
