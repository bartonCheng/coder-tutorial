# 使用 Docker 构建 PHP7.4 + Swoole + Redis 镜像

## 说明

- PHP7.4 + Swoole + Redis
- 所有的文件已经在 git 里写成脚本，[脚本地址](https://github.com/bartonCheng/docker-php7.4)

## 查看 docker 版本

```bash
docker --version
```

## 准备 Dockerfile

```bash
FROM php:7.4-fpm

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 更新安装依赖包和PHP核心拓展
RUN apt-get update && apt-get install -y \
        --no-install-recommends libfreetype6-dev libjpeg62-turbo-dev libpng-dev curl \
        && rm -r /var/lib/apt/lists/* \
        && docker-php-ext-configure gd \
        && docker-php-ext-install -j$(nproc) gd opcache pdo_mysql gettext sockets

# 安装 PECL 拓展，安装Redis，swoole
RUN pecl install redis \
    && pecl install swoole \
    && docker-php-ext-enable redis swoole

# 安装 Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

WORKDIR /data
```

### Dockerfile 文件中使用了一些指令关键字，以下是简要说明：

- FROM 指定哪个镜像作为你的基础镜像，我们是以官方的 php:7.4-fpm 作为基础镜像
- ENV 用于配置环境变量，在其他指令中可以直接引用 ENV 设置的环境变量
- RUN 执行命令并创建新的 Image Layer，看起来就跟 shell 命令一样
- WORKDIR 指定工作目录，如果使用 docker exec 进入容器时，默认目录就是指定的工作目录，如/data
- Dockerfile 文件还有很多指令，如 EXPOSE：暴露端口，VOLUME：定义匿名卷，等等，有兴趣的同学可以自行查找相关资料，本文不做过多讲解。

## 构建镜像

```bash
docker build -t hwphp:7.4-fpm .
```

## 查看镜像

```bash
docker images

```

## 运行

```bash
docker run -d --name myphp ysphp:7.4-fpm

# 进入镜像
docker exec -it myphp bash

# 查看php模块
php -m

# 验证swoole
php --ri swoole
```

## 常用的 docker 命令

```bash
#1.显示所有容器
docker ps -a[包括未运行] -q[仅显示编号]

#2.停止、重启、启动某一容器
docker stop|restart|start 容器id|容器名

#3.停止、重启、启动所有容器
docker stop|restart|start $(docker ps -a -q)

#4.获取容器ip
docker inspect 容器id

#5.容器开机启动
docker update --restart=always $(docker ps -a -q)

#6.删除容器[需要先停止运行]
docker rm 容器id|容器名

#7.删除镜像[需要先停止且删除所有关联的容器]
docker rmi 镜像id

#8.进入容器
docker exec -it 容器id|容器名 bash

#9.搜索镜像
docker search 镜像关键字

#10.下载镜像
docker pull 镜像名字:版本号

#11.查看本机所有docker镜像
docker images

#12.导出镜像
docker save -o 导出的镜像文件.tar 镜像名字:版本号

#13.导入镜像
docker load -i 镜像文件.tar

#14.从容器里面拷文件到宿主机
docker cp 容器名：要拷贝的文件在容器里面的路径   要拷贝到宿主机的相应路径
#如：
docker cp myphp:/home/data/test/js/test.js /opt

#15.从宿主机拷文件到容器里面
docker cp 要拷贝的文件路径 容器名：要拷贝到容器里面对应的路径
#如：
docker cp /opt/test.js myphp:/home/data/test/js
```
