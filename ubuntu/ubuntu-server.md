## 安装

- 安装系统首先需要刷写 u 盘启动盘，使用[rufus](http://rufus.ie/) 刷写工具
- Ubuntu 的[下载地址](https://ubuntu.com/download/server)
- 安装的时候最好有网线接口。安装完成可以设置成 wifi 网络

## 使用 wifi 进行连接网络

```bash
# 安装两个插件
sudo apt install wpasupplicant
sudo apt install network-manager

# 查看网络
ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp7s0 【注意这里】: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 1c:75:08:58:3b:e2 brd ff:ff:ff:ff:ff:ff
3: wlp6s0b1【注意这里】: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether ac:81:12:14:a0:2c brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.103/24 brd 192.168.1.255 scope global dynamic noprefixroute wlp6s0b1
       valid_lft 5576sec preferred_lft 5576sec
    inet6 2408:8215:3511:db20:9966:51b6:d2de:6a23/64 scope global temporary dynamic
       valid_lft 186936sec preferred_lft 84671sec
    inet6 2408:8215:3511:db20:ae81:12ff:fe14:a02c/64 scope global dynamic mngtmpaddr
       valid_lft 186936sec preferred_lft 100536sec
    inet6 fe80::ae81:12ff:fe14:a02c/64 scope link
       valid_lft forever preferred_lft forever
记住加粗的两个名字，过会使用

# 编辑文件 名称可能不同
cd /etc/netplan
sudo vim 00-installer-config.yaml
# 下面的名称需要对应加粗的名称
# This is the network config written by 'subiquity'
network:
  renderer: NetworkManager
  ethernets:
    enp7s0:
      dhcp4: true
  wifis:
    wlp6s0b1:
      dhcp4: true
      access-points:
        "CU_grN4_BK":
          password: "ta9at4cd"
  version: 2


# 进行应用
sudo netplan generate
sudo netplan apply
```

## 树莓派设置 wifi 连接配置文件

- 树莓派需要先使用网线进行连接

```bash
# 查看配置文件
cat /etc/netplan/50-cloud-init.yaml

# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    renderer: NetworkManager
    ethernets:
        eth0:
            dhcp4: true
            optional: true
    wifis:
      wlan0:
        dhcp4: true
        access-points:
          "CU_grN4_BK":
            password: "ta9at4cd"
    version: 2
```

## 使用 Ubuntu 笔记本盒盖不影响系统

```bash
# 进入目录
cd /etc/systemd/
# 修改
sudo cp logind.conf logind.conf.bak
sudo vim logind.conf

[Login]
#NAutoVTs=6
#ReserveVT=6
#KillUserProcesses=no
#KillOnlyUsers=
#KillExcludeUsers=root
#InhibitDelayMaxSec=5
#HandlePowerKey=poweroff
#HandleSuspendKey=suspend
#HandleHibernateKey=hibernate
#HandleLidSwitchExternalPower=suspend
#HandleLidSwitch=suspend
添加一行
HandleLidSwitch=ignore
#HandleLidSwitchDocked=ignore
#PowerKeyIgnoreInhibited=no
#SuspendKeyIgnoreInhibited=no
#HibernateKeyIgnoreInhibited=no
#LidSwitchIgnoreInhibited=yes
#HoldoffTimeoutSec=30s
#IdleAction=ignore
#IdleActionSec=30min
#RuntimeDirectorySize=10%
#RemoveIPC=yes
#InhibitorsMax=8192
#SessionsMax=8192
```

## 阿里源镜像

[官网地址](https://developer.aliyun.com/mirror/)

## ufw 防火墙配置

防火墙是监视和过滤进出流量的工具，Ubuntu 自带的防火墙配置工具被称为 UFW (Uncomplicated Firewall)。UFW 是一个用来管理 iptables 防火墙规则的用户友好的前端工具。它的主要目的就是为了使得管理 iptables 更简单。

```bash
# 检查 UFW 的状态
sudo ufw status verbose
sudo ufw status

# 激活ufw
sudo ufw enable

# 使用默认的安全设置，阻止一切外部连接，放行一切内部连接。
sudo ufw default deny

# 开放 禁用某些服务
sudo ufw allow | deny [service]

#允许外部访问3690端口(svn)
sudo ufw allow 3690

# 守护程序监听了7722
sudo ufw allow 7722/tcp

# 允许此IP访问所有的本机端口
sudo ufw allow from 192.168.1.111

# 允许指定的IP段访问特定端口
sudo ufw allow proto tcp from 192.168.0.0/24 to any port 22

# 删除上面建立的某条规则，比如删除svn端口就是 sudo ufw delete allow 3690
sudo ufw delete allow smtp

# 开启关闭防火墙
sudo ufw enable | disable

# 重启防火墙
sudo ufw reload

# 列举出你系统上所有的应用配置
sudo ufw app list
```

## netstat

```bash
# 安装 netstat
sudo apt install net-tools

# 查看指定端口
netstat -ap | grep 6006

# 关闭使用这个端口的程序
sudo kill -9 PID号
```

## 树莓派使用网线进行电脑连接

- 使用 win10 64 位
- 网线
- 上电树莓派

### 使用用 Advanced IP Scanner

- 这个软件是免费的
- 使用软件扫描，找到 192.168.137.154 或者 192.168.137.\*

### 配置网络连接

- 配置 WLAN 的属性-共享-使用允许其他用户共享
- 配置以太网 - 属性
- ip 192.168.137.154 -这里需要和扫描软件的 IP 一致
- 子网掩码 255.255.255.0
- DNS 的服务器 192.168.137.1

### 连接

```bash
# 查看本地连接
arp -a

# 找到树莓派的ip 192.168.137.52
# 使用xshell连接
```

## 使用

```bash
# vim中权限不足时不用退出而强制保存
:w !sudo tee %
# ctrl + z 可以暂时退出屏幕
# fg 命令可以调回窗口
```

## 解决 ifconfig 不能使用

```bash
#这是新的替代工具，包含：arp,ifconfig,netstat.rarp,nameif,route
sudo apt-get install net-tools
```

## 清理系统

```bash
#清理已经卸载的缓存包
$ sudo apt autoclean
#清理软件安装包
$ sudo apt clean
# 清理系统孤儿包
$ sudo apt autoremove
# 更新软件
$ sudo apt update
#更新安装
$ sudo apt upgrade
```

## 文件系统操作

```bash
# 查看内存剩余
free -m

# 查看进程
ps

# 查看占用内存前三
top

# 检测cpu
vmstat 2 3

# 查看当前空间大小
df -h

```

## 文件/角色权限

- 文件

```bash
# 文件权限
$ chmod -R 755 files
$ chmod -R 644 files
$ chmod -R a+x files
$ chmod -R u+x files
$ chmod 755 file1 file2
$ chmod 700 file1 file2
$ chmod 644 file1 file3
$ chmod 600 file1 file3

# 操作文件内容
$ cat file1
$ cat file1 | grep sometings
# 写入到文件，ctrl+D结束
$ cat >> file2
$ cat file2
#合并文件
$ cat file1 file 2 >> file3
# 分页查看文件内容
$ more file1 file2
$ less file1 file2
# 命令输出到文件
$ ls -al > out.txt
```

- 角色

```bash
#角色
$ chown -R www-data:www-data files
$ chown -R apache:apache files
$ chwon www:www file1 file3
$ chown kevin:kevin /var/src/*

# 查看当前用户
id
users

```

## 创建/复制/删除

```bash
#  创建一个文件夹
$ mkdir Keivn
#创建一个或者多个文件
$ touch file1 file2 file3

#复制文件
$ cp orifile1 desfile2

#重命名
$ mv file1 file2

#删除文件
$ rm  file1.*
$ rm  file
# 删除文件夹
$ rmdir files
$ rmdir file.*
```
