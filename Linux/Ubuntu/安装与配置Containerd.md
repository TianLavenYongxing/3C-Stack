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
### 基础验证
查看服务状态
```bash
systemctl status containerd
```
拉取镜像测试
```bash
crictl pull docker.io/library/nginx:latest
```
