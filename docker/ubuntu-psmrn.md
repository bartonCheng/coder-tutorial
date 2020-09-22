#使用 Docker-compose 编排一个 PHP + Nginx + Mariadb + Redis + Swoole 环境

## 说明

- PHP + Nginx + Mariadb + Redis + Swoole
- Docker-compose
- [Docker-compose 文档地址](https://docs.docker.com/compose/)

## Docker Compose 安装

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 查看版本
docker-compose --version
```

## 项目结构

```bash
├── docker-compose.yml //编排配置文件
├── Dockerfile //Dockerfile文件,编译自定义镜像
├── LICENSE
├── mariadb //MariaDB配置相关目录
│   ├── conf.d
│   │   └── my.cnf
│   ├── logs
│   └── mysql
├── nginx //Nginx配置相关目录
│   ├── conf.d
│   │   └── default.conf
│   ├── logs
│   └── nginx.conf
├── php  //PHP配置相关目录
│   ├── logs
│   ├── php.ini
│   └── www.conf
├── README.md
├── redis //Redis配置相关目录
│   ├── db
│   └── logs
└── www //web访问目录
    ├── index.html //测试nginx文件
    ├── index.php //测试swoole服务文件
    ├── mysql.php //测试mariadb连接文件
    └── redis.php //测试redis连接文件
```

## nginx 配置

```bash
server {
        listen 80;
        server_name  "";

        index index.html index.htm index.php;
        root /usr/share/nginx/html;

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }

        location ~ \.php$ {
            fastcgi_pass   php:9000;  #容器名:端口
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }

        access_log  /var/log/nginx/test.log  main;
 }
```

## 启动服务

```bash
docker-compose up -d
```

## 常用命令

```bash
# 启动服务：
docker-compose up -d

# 停止并卸载服务：
docker-compose down

# 查看容器：
docker-compose ps

# 进入容器：
docekr-compose exec $1 $2

# $1 docker-compose.yml文件services中定义的服务名称

# $2 根据基础镜像服务器决定，一般apline为/bin/sh，其他为/bin/bash
```
