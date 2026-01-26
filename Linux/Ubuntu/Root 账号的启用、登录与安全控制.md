## 默认情况说明（重要）

Ubuntu **默认禁用 root 直接登录**，但并非没有 root：

- root 账号存在，但 **没有密码**
- 通过 `sudo` 临时获取 root 权限（官方推荐方式）

只有在**明确需要**（如自动化、封闭环境、运维规范要求）时，才建议启用 root 登录。
### 使用 sudo 设置 root 密码

```bash
sudo passwd root
```
### 切换到 root 验证
```bash
su - root
```
能成功进入，说明 root 已启用。
## 允许 root 通过 SSH 登录（可选，慎用）

### 编辑 SSH 配置

```bash
sudo vi /etc/ssh/sshd_config
```

找到或新增以下配置：

```bash
PermitRootLogin yes
```
### 重启 SSH 服务
```bash
sudo systemctl restart ssh
```
或：
```bash
sudo systemctl restart sshd
```
## 推荐的更安全做法（生产环境建议）

### 方案 A：允许 root 仅密钥登录
```text
PermitRootLogin prohibit-password
```
含义：
- root **不能密码登录**
- 只能通过 SSH key（更安全）
### 方案 B：不启用 root，使用 sudo（官方推荐）
```bash 
sudo -i
```
等价于：
```bash
sudo - root
```
无需修改任何系统配置。
## 关闭 root 账号（可回滚）

如果你之后想恢复默认状态：
### 锁定 root
```bash
sudo passwd -l root
```
### 禁止 SSH root 登录
```text
PermitRootLogin no
```
重启 SSH 即可。