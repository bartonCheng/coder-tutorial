# Ubuntu 下使用 Samba 共享文件

## Samba 使用

- 安装 samba
- 设置 ufw 防火墙
- 挂载 u 盘

## 安装 Samba

```bash
# 安装
sudo apt install samba

# 查看版本
samba -V

# 设置samba
cd /etc/samba/
sudo cp smb.conf smb.conf.bak
sudo vim smb.conf

# 添加内容
[global]
security = user
[share]
   browseable = yes
   path = /home/dk/share
   valid users = dk
   public = yes
   available = yes

# 启动服务
sudo service smbd start

# 设置用户密码
sudo smbpasswd -a dk
# 密码

# 打开mac文件
# 使用 command + K
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
