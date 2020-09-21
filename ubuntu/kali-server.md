## 树莓派设置 kali 的 wifi 连接配置文件

- 安装最新的 kali 镜像
- [KALI LINUX ARM IMAGES](https://www.offensive-security.com/kali-linux-arm-images/)
- 使用 Kali Linux RaspberryPi 2 (v1.2), 3 and 4 (64-Bit)

## 刷写镜像到 SD 卡

- [参考树莓派设置](./ubuntu-server.md)

## 使用网线连接树莓派

- [参考树莓派设置](./ubuntu-server.md)

## 更改或内到 kali 源

```bash
vi /etc/apt/sources.list

deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib

sudo apt update
sudo apt upgrade

sudo apt clean
sudo apt autoclean

```

## 设置日期

```bash
# 前两条是把默认的UTC 改为CST
# 第三条是同步时间
cp /usr/share/zoneinfo/GMT /etc/localtime
ln -sf /usr/share/zoneinfo/Asia/Shanghai    /etc/localtime
sudo ntpdate cn.pool.ntp.org
```

## kali 设置 wifi 连接

```bash
# 创建连接文件
cd /etc/wpa_supplicant/
vim wpa_supplicant.conf

# 文件内容
network={
  ssid="CU_grN4_BK"
  psk="ta9at4cd"
}

# 编辑 interfaces 文件
cd /etc/network/
sudo vim interfaces
# 增加下面内容
auto wlan0
allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp

# 重新启动
sudo reboot
```

## 修改密码

```bash
# 修改用户kali密码
sudo passwd kali

# 修改根密码
sudo passwd
```
