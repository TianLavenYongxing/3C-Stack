

## 配置

### 安装必要工具

```bash
# 更新系统
apt update && apt upgrade -y

# 安装必要工具
apt install -y nginx dpkg-dev apt-utils gnupg2
```

## 创建仓库目录结构

```bash
# 创建仓库根目录
mkdir -p /var/www/html/apt-repo

# 创建标准 APT 结构
mkdir -p /var/www/html/apt-repo/dists/noble/main/binary-amd64
mkdir -p /var/www/html/apt-repo/pool/main

# 创建配置目录
mkdir -p /var/www/html/apt-repo/conf
```

## 添加软件包到仓库

```bash
apt download kubeadm=1.35.0-1.1 kubelet=1.35.0-1.1 kubectl=1.35.0-1.1
apt download kubernetes-cni=1.8.0-1.1 cri-tools=1.35.0-1.1
```

### 生成 Packages 索引

```bash
cd /var/www/html/apt-repo

# 生成 Packages 文件
dpkg-scanpackages -m pool/main /dev/null > dists/noble/main/binary-amd64/Packages

# 压缩 Packages 文件
gzip -k -f dists/noble/main/binary-amd64/Packages
```

### 创建 Release 文件

```bash
cd /var/www/html/apt-repo

# 创建简单的 Release 文件
cat > dists/noble/Release << EOF
Origin: My Local Repository
Label: Local APT Repo
Suite: noble
Codename: noble
Version: 24.04
Architectures: amd64
Components: main
Description: Custom local APT repository
Date: $(date -Ru)
EOF

# 或者使用 apt-ftparchive 生成更完整的 Release
apt-ftparchive release dists/noble/ > dists/noble/Release
```

## 配置 Web 服务器

```bash
cat > /etc/nginx/sites-available/apt-repo << 'EOF'
server {
    listen 80;
    server_name apt.example.com;  # 修改为你的域名或IP
    
    root /var/www/html/apt-repo;
    index index.html;
    
    location / {
        autoindex on;
        autoindex_exact_size off;
        autoindex_localtime on;
        
        # 允许 apt 访问
        if ($request_method = GET) {
            add_header Access-Control-Allow-Origin *;
        }
    }
    
    # 提高大文件传输性能
    client_max_body_size 100M;
    
    access_log /var/log/nginx/apt-repo.access.log;
    error_log /var/log/nginx/apt-repo.error.log;
}
EOF

# 启用站点
ln -sf /etc/nginx/sites-available/apt-repo /etc/nginx/sites-enabled/
nginx -t
systemctl reload nginx
```

## 配置 GPG 签名（可选但推荐） 未测试

### 生成 GPG 密钥

```bash
# 安装 GPG
apt install -y gnupg2

# 生成密钥（非交互式）
cat > /tmp/gpg-keygen << 'EOF'
%echo Generating a basic OpenPGP key
Key-Type: RSA
Key-Length: 4096
Subkey-Type: RSA
Subkey-Length: 4096
Name-Real: APT Repository
Name-Comment: Local APT Repo
Name-Email: apt@example.com
Expire-Date: 0
%commit
%echo Done
EOF

gpg --batch --generate-key /tmp/gpg-keygen

# 导出公钥
gpg --armor --export apt@example.com > /var/www/html/apt-repo/apt-repo.gpg.key
```

### 签名仓库

```bash
cd /var/www/html/apt-repo

# 签名 Release 文件
gpg --default-key apt@example.com -abs -o dists/noble/Release.gpg dists/noble/Release
gpg --default-key apt@example.com --clearsign -o dists/noble/InRelease dists/noble/Release
```

## 客户端配置

### 添加仓库源

```bash
# 在客户端机器上执行

# 1. 添加 GPG 密钥
curl -fsSL http://your-server-ip/apt-repo/apt-repo.gpg.key | gpg --dearmor -o /usr/share/keyrings/local-apt-repo.gpg

# 2. 添加源到 sources.list.d
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/local-apt-repo.gpg] http://your-server-ip/apt-repo noble main" | tee /etc/apt/sources.list.d/local-repo.list

# 3. 更新并测试
apt update
apt-cache policy
```

###  不安全的仓库（无签名）

```bash
# 如果不用 GPG 签名
echo "deb [trusted=yes] http://your-server-ip/apt-repo noble main" | tee /etc/apt/sources.list.d/local-repo.list
apt update
```

## 自动化管理脚本

### 添加新包的脚本

```bash
cat > /usr/local/bin/update-apt-repo << 'EOF'
#!/bin/bash
REPO_DIR="/var/www/html/apt-repo"
cd "$REPO_DIR"

echo "正在更新 APT 仓库..."

# 更新 Packages 文件
dpkg-scanpackages -m pool/main /dev/null > dists/noble/main/binary-amd64/Packages
gzip -k -f dists/noble/main/binary-amd64/Packages

# 更新 Release 文件
apt-ftparchive release dists/noble/ > dists/noble/Release

# 重新签名
if [ -f "$HOME/.gnupg/secring.gpg" ]; then
    gpg --default-key apt@example.com -abs -o dists/noble/Release.gpg dists/noble/Release
    gpg --default-key apt@example.com --clearsign -o dists/noble/InRelease dists/noble/Release
fi

echo "APT 仓库更新完成！"
EOF

chmod +x /usr/local/bin/update-apt-repo
```

### 添加包的快捷方式

```

```

