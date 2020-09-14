## 简介

之前安装 ngrok 的时候，笔记做的不够详细，很多东西现在居然没有实现成功。现在重新整理记录。

- ngrok 作为转发服务器
- 对内网环境、无外网 ip 的计算机进行远程连接。
- 用于临时网站搭建并分配二级域名。
- 二次开发的本地调试。

## 转发服务器安装

### 泛域名解析

![](https://img2020.cnblogs.com/blog/2007691/202009/2007691-20200908204258535-814943799.png)

- 注意，这里都是 a 记录类型的解析
- 只有解析成功，才能配置通

<a href="http://www.youtube.com/watch?feature=player_embedded&v=YOUTUBE_VIDEO_ID_HERE
" target="_blank"><img src="http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg"
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>

### 使用脚本一键安装

[ngrokd-server](https://github.com/bartonCheng/ngrokd-server)

```bash
# 使用的Ubuntu18.04版本
apt update
apt -y upgrade
apt install git
git clone git@github.com:bartonCheng/ngrokd-server.git
cd ngrokd-server/
sh ngrokd-ubuntu.sh
全新安装
生成客户端
启动服务即可
```

### ngrok 后台运行

- 方法一 screen

```bash
$ sudo apt-get install screen
# 创建新屏幕
$ screen -S server
$ screen -S client
# 退出当前
使用快捷键ctrl+A->D
# 查看后台screen进程
screen -ls
# 重新连接后台进程
screen -r 1826
```

### 使用配置文件

```bash
#settings.cfg
server_addr: "ceshi.whbaqn.com:3399"
tunnels:
  http:
    subdomain: "www"
    auth: "dkkevin:admin999"
    proto:
     http: 80
  https:
    subdomain: "www"
    proto:
     https: 443
  ssh:
    remote_port: 50000
    proto:
     tcp: 192.168.1.120:22
  test:
    hostname: "test.ceshi.whbaqn.com:8080"
    proto:
     http: 80
  app:
    hostname: "app.ceshi.whbaqn.com:8080"
    proto:
     http: 80
```

### 启动客户端

```bash
$ ngrok -config=ngrok.cfg start http https test app
$ ngrok -config=ngrok.cfg start app
```

## let's Encrypt 加密证书实现 ssl

### 安装加密验证工具

[官方网站](https://letsencrypt.org/zh-cn/getting-started/)

```bash
apt install certbot

```

### 验证

```bash
certbot certonly -d 'ceshi.whbaqn.com' -d '*.ceshi.whbaqn.com' --manual --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory
```

### 配置解析

![](https://img2020.cnblogs.com/blog/2007691/202009/2007691-20200908205918019-623921634.png)

### 续约

```bash
crontab -e
# 定时任务
00 03 1 * * /usr/bin/certbot renew --quiet
05 03 1 * * /usr/sbin/nginx -s reload
```

### ngrok 服务端的配置

```bash
./ngrokd -domain="ceshi.whbaqn.com" -tunnelAddr=":3399" -tlsKey="/etc/letsencrypt/live/ceshi.whbaqn.com/privkey.pem" -tlsCrt="/etc/letsencrypt/live/ceshi.whbaqn.com/fullchain.pem"
```

## 使用 ufw 防火墙

- 这是 Ubuntu 的服务器自带的防火墙工具，centos 需要自己安装
- 启用防火墙可以阻挡大部分攻击
- ufw 是一个工具，操作的本质也是 iptables
- iptables 其实不是真正的防火墙，我们可以把它理解成一个客户端代理，用户通过 iptables 这个代理，将用户的安全设定执行到对应的"安全框架"中，这个"安全框架"才是真正的防火墙，这个框架的名字叫 netfilter
- [官方文档](https://help.ubuntu.com/community/UFW)

### 用法

```bash
# 启用
ufw enable
# 禁用
ufw disable
# 查看当前防火墙规则
ufw status
# 禁止80端口连接
ufw deny 80/tcp
# 允许
ufw allow port
# 删除规则
ufw delete allow 80/tcp
# 使用默认 阻挡一切外部连接，放行一切内部外连
sudo ufw default deny
```

## Linux 定时任务

- 定时任务的守护进程
- 定时任务分系统定时任务和用户的定时任务
- -u 参数是指定用户的定时任务

### 系统定时任务

```bash
# 列出目前的定时任务
crontab -l
crontab -u root -l

# 编辑定时任务
crontab –e
crontab -u root -e
# 删除定时任务
crontab –i
crontab -u root -i

# 查看定时任务的例子
cat /etc/crontab

# 时间格式
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 7) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12)
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```
