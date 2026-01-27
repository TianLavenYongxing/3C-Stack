
### 查看配置文件
```bash
cd /etc/netplan/
cat 50-cloud-init.yaml
network:
    ethernets:
        ens33:
          dhcp4: true
    version: 2
```
修改和增加配置
```json
network:
    ethernets:
        ens33:
          dhcp4: no
          addresses:
            - 192.168.113.101/24
    version: 2
```
