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
1 生成默认配置文件