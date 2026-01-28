### 基本语法
```bash
scp [选项] 源路径 目标路径
```
路径格式：
```text
用户@主机:路径
```
最常见 4 种用法:
1. 本地 → 远程（传文件）
```bash
scp file.txt user@192.168.1.10:/opt/
```
2. 远程 → 本地（拉文件）
```bash
scp user@192.168.1.10:/opt/file.txt .
```
3. 本地 → 远程（传目录，**必须 -r**）
```bash
scp -r ./jumpserver user@192.168.1.10:/opt/
```
4. 远程 ↔ 远程（跳板机常用）
```bash
scp user1@host1:/data/a.log user2@host2:/backup/
```
### 核心参数详解（非常重要）
| 参数   | 作用     | 你什么时候用 |
| ---- | ------ | ------ |
| `-r` | 递归目录   | 传文件夹   |
| `-P` | SSH 端口 | 非 22   |
| `-i` | 指定私钥   | 免密登录   |
| `-C` | 压缩     | 慢网络    |
| `-v` | 调试     | 连接失败   |
| `-p` | 保留时间权限 | 运维迁移   |
| `-q` | 静默     | 脚本     |
### 示例
非 22 端口：
```bash
scp -P 2222 file.txt user@1.1.1.1:/opt/
```
使用 SSH 私钥：
```bash
scp -i ~/.ssh/id_rsa file.txt user@host:/opt/
```
目录 + 压缩 + 私钥:
```bash
scp -rC -i key.pem ./data user@host:/data
```
### 进阶技巧
显示传输进度（建议）
```bash
scp -v file.iso user@host:/data
```
限速（防止打满带宽）
```bash
scp -l 2048 file.iso user@host:/data
```
> 单位：**Kbit/s**

免交互（脚本）
```bash
scp -q file.txt user@host:/opt/
```