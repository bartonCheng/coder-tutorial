# Ubuntu 下使用 Samba 共享文件

## Samba 使用

- 安装 samba
- 设置 ufw 防火墙
- 挂载 u 盘

## 安装 Samba

```bash
# 启动服务
sudo service smbd start
```

## 设置 ufw

```bash
# 允许samba通过
sudo ufw allow samba

# 重启服务
sudo service smbd restart
```

## 挂载 u 盘

### 常用命令

```bash
# 查看本地挂载点
df -h

# 查看本地硬盘
fdisk -l

# 挂载u盘
sudo mount -t ntfs /dev/sdc5 /home/dk/share

# 卸载u 盘
sudo umount /home/dk/share

# 强制卸载
sudo umount -l /home/dk/share
```
