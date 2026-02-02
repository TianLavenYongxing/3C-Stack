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

### 命令和使用
```text
ctr images --help
NAME:
   ctr images - Manage images
USAGE:
   ctr images command [command options] [arguments...]
COMMANDS:
   check                    Check existing images to ensure all content is available locally
   export                   Export images
   import                   Import images
   list, ls                 List images known to containerd
   mount                    Mount an image to a target path
   unmount                  Unmount the image from the target
   pull                     Pull an image from a remote
   push                     Push an image to a remote
   prune                    Remove unused images
   delete, del, remove, rm  Remove one or more images by reference
   tag                      Tag an image
   label                    Set and clear labels for an image
   convert                  Convert an image
   usage                    Display usage of snapshots for a given image ref
OPTIONS:
   --help, -h  show help
```
