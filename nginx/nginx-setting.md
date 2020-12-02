# 安装

## 介绍

- Http 代理，反向代理
- 负载均衡，Nginx 提供的负载均衡策略有 2 种：内置策略和扩展策略。内置策略为轮询，加权轮询，Ip hash。扩展策略，就天马行空，只有你想不到的没有他做不到的啦

## 安装

```bash
# 安装
brew install nginx
# 重新安装
brew reinstall nginx
```

## 配置文件

```bash
# nginx.conf 配置文件位置：/usr/local/etc/nginx/nginx.conf

# 配置文件在本目录
cp nginx.conf /usr/local/etc/nginx/nginx.conf

# nginx 安装目录：/usr/local/Cellar/nginx
# nginx 网站目录：/usr/local/var/www
```

## 操作

```bash
#  启动
sudo nginx

# 重启
sudo nginx -s reload

# 退出
sudo nginx -s stop
```
