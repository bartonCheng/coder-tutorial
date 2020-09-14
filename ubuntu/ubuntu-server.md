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

## 使用

```bash
# vim中权限不足时不用退出而强制保存
:w !sudo tee %
# ctrl + z 可以暂时退出屏幕
# fg 命令可以调回窗口
```
