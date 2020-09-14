## Docker 使用参考

### 安装 Centos 7.0

#### 配置

```bash
# 因为装的是新的系统所以先 升级最新依赖
yum update
# 【命令】查看内核版本
uname -r
# docker 安装系统要求内核是 3.1 以上
# 【命令】查看系统版本
cat /etc/os-release
# 结果
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

### 安装

- [Docker 帮助文档](https://docs.docker.com/engine/install/)

```bash
# 卸载旧的版本
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 安装依赖包
sudo yum install -y yum-utils
# 设置镜像仓库
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
# 使用国内的镜像【推荐】
sudo yum-config-manager \
    --add-repo \
      http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 更新系统镜像缓存
yum makecache fast
# 按照docker 相应的 docker-ce ce是社区版本ee商业版本
sudo yum install docker-ce docker-ce-cli containerd.io
# 启动docker
sudo systemctl start docker
# 查看docker 版本信息
docker version
# 结果
Client: Docker Engine - Community
 Version:           19.03.9
 API version:       1.40
 Go version:        go1.13.10
 Git commit:        9d988398e7
 Built:             Fri May 15 00:25:27 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.9
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.10
  Git commit:       9d988398e7
  Built:            Fri May 15 00:24:05 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
# 测试helloworld
sudo docker run hello-world
# 查看本地镜像
docker images
# 结果
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        4 months ago        13.3kB

# 卸载docker依赖 -- 如果需要卸载才执行
sudo yum remove docker-ce docker-ce-cli containerd.io

# 删除目录 也是docker的默认工作路径
sudo rm -r -f /var/lib/docker
```

### 配置阿里云镜像加速

- [登陆阿里云 容器服务](https://cr.console.aliyun.com/cn-qingdao/instances/mirrors)
- 找到镜像加速地址
- 配置使用

```bash
# 配置
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://t3thelnd.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```

## 底层原理

### Docker 是怎么工作的

Docker 是由 Server -Cli 两个部分 Docker-cli 发送指令给 Docker-Server 执行操作。

### Docker 常用命令

```bash
# 帮助命令
docker commnand --help // 查看命令信息
docker info // docker 详细信息
docker version // docker 概要信息

# 镜像命令

# 容器命令
docker run -it -d -p 8080:80 -v /local/myfiles:/etc/local -e APP_KEY=TRUE --name nginx01 nginx /bin/bash
-it 交互
-d 后台
-e 传递变量
-p 指定端口
-v 指定数据卷

docker logs 容器id # 查看容器日志
dokcer inspect 容器id # 查看容器的详细配置
```

#### [docker 帮助文档](https://docs.docker.com/reference/)

### 镜像命令

#### 查看本地主机的所有镜像

```bash
docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        4 months ago        13.3kB
REPOSITORY 镜像仓库源
TAG  镜像标签
IMAGE ID  镜像ID
CREATED  镜像创建时间
SIZE 镜像的大小

# 选项
-a 显示全部镜像
-q 只显示镜像id
```

#### 搜索镜像

```bash
docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9553                [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3471                [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   700                                     [OK]

# 选项
-f=STARS=3000 通过收藏过滤
```

#### 拉取命令

```bash
docker pull mysql
Using default tag: latest # 默认使用最新的版本
latest: Pulling from library/mysql
afb6ec6fdc1c: Pull complete # 分层下载 docker image的核心
0bdc5971ba40: Pull complete
97ae94a2c729: Pull complete
f777521d340e: Pull complete
1393ff7fc871: Pull complete
a499b89994d9: Pull complete
7ebe8eefbafe: Pull complete
597069368ef1: Pull complete
ce39a5501878: Pull complete
7d545bca14bf: Pull complete
211e5bb2ae7b: Pull complete
5914e537c077: Pull complete
Digest: sha256:a31a277d8d39450220c722c1302a345c84206e7fd4cdb619e7face046e89031d # 签名信息
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # 真实地址 === docker pull mysql
```

#### 指定版本下载

```bash
docker pull mysql:5.7
5.7: Pulling from library/mysql
afb6ec6fdc1c: Already exists // 联合文件系统
0bdc5971ba40: Already exists
97ae94a2c729: Already exists
f777521d340e: Already exists
1393ff7fc871: Already exists
a499b89994d9: Already exists
7ebe8eefbafe: Already exists
4eec965ae405: Pull complete
a531a782d709: Pull complete
270aeddb45e3: Pull complete
b25569b61008: Pull complete
Digest: sha256:d16d9ef7a4ecb29efcd1ba46d5a82bda3c28bd18c0f1e3b86ba54816211e1ac4
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

#### 删除镜像

```bash
docker rmi -f a4fdfd462add
Untagged: mysql:5.7
Untagged: mysql@sha256:d16d9ef7a4ecb29efcd1ba46d5a82bda3c28bd18c0f1e3b86ba54816211e1ac4
Deleted: sha256:a4fdfd462add8e63749aa08ff0044b13d342a042965f1ec6744586cda10dfce9
Deleted: sha256:637f0ff7e591e53fe997c634cf10e63ab810dd1d6cb587ce46a57f753c36bdbf
Deleted: sha256:65ba4d5ac7eb5218cfb4be1e7807584425c19f47606bc1e6d53e050d480d9581
Deleted: sha256:7d0236d50948d993a686c69889c6f016a8da89b8557c5e0eaf6af145ea5877cb
Deleted: sha256:a6219b1270405f43892a7a12895ae1e0ccff307d162cf0025df3ed87f511754b
```

#### 批量删除

```bash
docker rmi -f $(docker images -qa)
Untagged: mysql:latest
Untagged: mysql@sha256:a31a277d8d39450220c722c1302a345c84206e7fd4cdb619e7face046e89031d
Deleted: sha256:30f937e841c82981a9a6363f7f6f35ed6b9d5e3f16df50a72207e4a2a389983f
Deleted: sha256:8a5e032615340d8936e0e3707a39ce3da51dc952368176818f879e2f868b535b
Deleted: sha256:c74673a735ca31b9b5162808ab451a8b20876a15e16a7899f2101f3c9b82df60
Deleted: sha256:430365c8e22a9207dca4638c523dc82163bca3ab8a335a71147af41d1551561f
Deleted: sha256:1ede41b1dbec1a5e4385200b62283ffb25c425275530ea9e9cc36b921186cd08
Deleted: sha256:2f6badb9fd9965261d3463591f8af4afddf5f141456de83dc994690ae64b34eb
Deleted: sha256:37803884320881cd931c77dea2ee4d8a7231dfed5a02dc595e6046ffacfa6e1b
Deleted: sha256:cefc9066dc1aa84f6cddead1bb5a8c590e8368d56fb65694e8783d70791bec20
Deleted: sha256:3bfbd2dd4507386ce56fd731b3c97d10bc058e6aa478f901466da69108db50e1
Deleted: sha256:9652363dd4c1146b3f9a519800a9f379adf0b6c4f9aece1ffe965dce5f52a8ca
Deleted: sha256:0ed190016efa0f19bcc5f1d66ffffc7b09716f3c57bcc5de74a4ce217af92278
Deleted: sha256:8399fb13d72603fdc8781075672ee25fedf8384f6721639a70dd3533250ed9e4
Deleted: sha256:ffc9b21953f4cd7956cdf532a5db04ff0a2daa7475ad796f1bad58cfbaf77a07
Untagged: hello-world:latest
Untagged: hello-world@sha256:6a65f928fb91fcfbc963f7aa6d57c8eeb426ad9a20c7ee045538ef34847f44f1
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
```

### 容器命令

> 有了镜像才能创建容器

```bash
# 下载centos
docker pull centos
```

#### 新建容器并启动

```bash
docker run --help
# 参数说明
--name="name" 容器名字 区分容器
-d 使用后台方式运行
-i -t 使用交互方式运行
-P 指定端口 -p 8080:8000
  -P 主机端口:容器端口 常用
-p 随机指定端口

# 测试
[root@iZ284pvwwnfZ ~]# docker run -it centos /bin/bash # 启动进入容器，基础镜像很多命令不完善
[root@189add9e4171 /]# 已经进入了容器
exit 退出容器
```

#### 列出所有运行的容器

```bash
docker ps

-n=1 # 显示最近运行的容器个数
-q # 只显示容器的编号
[root@iZ284pvwwnfZ ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
# 列出历史运行过的容器
[root@iZ284pvwwnfZ ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
189add9e4171        centos              "/bin/bash"         2 minutes ago       Exited (0) 59 seconds ago                       wonderful_mahavira
3d56e66407fc        bf756fb1ae65        "/hello"            3 hours ago         Exited (0) 3 hours ago                          exciting_joliot
```

#### 删除容器

```bash
docker rm 容器id
docker rm -f $(docker ps -aq )
docker ps -aq |xargs docker rm # 删除所有容器
```

#### 退出容器

```bash
exit 直接退出
Ctrl +p +q 容器不停止退出
```

```bash
# 启动容器
docker start 容器id
# 重启容器
docker restart 容器id
# 停止容器
docker stop 容器id
# 杀掉容器
docker kill 容器id
[root@iZ284pvwwnfZ ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
977e90428435        centos              "/bin/bash"         7 seconds ago       Exited (0) 4 seconds ago                       wizardly_ramanujan
[root@iZ284pvwwnfZ ~]# docker start 977
977
[root@iZ284pvwwnfZ ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
977e90428435        centos              "/bin/bash"         27 seconds ago      Up 13 seconds                           wizardly_ramanujan
[root@iZ284pvwwnfZ ~]# docker stop 977
977
[root@iZ284pvwwnfZ ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

### 常用的进阶命令

#### 后台启动

```bash
docker run -d centos
# 如果容器没有前台应用去使用它，容器就会自杀。所以后天启动需要有其他容器配合
```

#### 查看日志

```bash
docker logs -f -t --tail 10 容器id
# 自己编写shell脚本
[root@iZ284pvwwnfZ ~]# docker run -d centos /bin/sh -c "while true;do echo 123456;sleep 1;done"
bafdbd85ecbf98909af2999dee86430a177f54052a4c0bd8acdf0a703f9540b0
[root@iZ284pvwwnfZ ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
bafdbd85ecbf        centos              "/bin/sh -c 'while t…"   6 seconds ago       Up 5 seconds                            frosty_chaplygin
[root@iZ284pvwwnfZ ~]# docker logs -tf --tail 10 baf
2020-05-28T06:10:08.894888649Z 123456
2020-05-28T06:10:09.896947206Z 123456
2020-05-28T06:10:10.898947489Z 123456
2020-05-28T06:10:11.901000950Z 123456
2020-05-28T06:10:12.903012725Z 123456
2020-05-28T06:10:13.905012761Z 123456
```

#### 查看容器中的进程信息

```bash
docker top 容器id
[root@iZ284pvwwnfZ ~]# docker top baf
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                25916               25887               0                   14:10               ?                   00:00:00            /bin/sh -c while true;do echo 123456;sleep 1;done
root                26213               25916               0                   14:13               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
```

#### 查看镜像源配置数据

```bash
docker inspect 容器id
docker inspect baf
[
    {
        "Id": "bafdbd85ecbf98909af2999dee86430a177f54052a4c0bd8acdf0a703f9540b0",
        "Created": "2020-05-28T06:10:01.495261097Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo 123456;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 25916,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-05-28T06:10:01.896288811Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:470671670cac686c7cf0081e0b37da2e9f4f768ddc5f6a26102ccd1c6954c1ee",
        "ResolvConfPath": "/var/lib/docker/containers/bafdbd85ecbf98909af2999dee86430a177f54052a4c0bd8acdf0a703f9540b0/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/bafdbd85ecbf98909af2999dee86430a177f54052a4c0bd8acdf0a703f9540b0/hostname",
        "HostsPath": "/var/lib/docker/containers/bafdbd85ecbf98909af2999dee86430a177f54052a4c0bd8acdf0a703f9540b0/hosts",
        "LogPath": "/var/lib/docker/containers/bafdbd85ecbf98909af2999dee86430a177f54052a4c0bd8acdf0a703f9540b0/bafdbd85ecbf98909af2999dee86430a177f54052a4c0bd8acdf0a703f9540b0-json.log",
        "Name": "/frosty_chaplygin",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/95b98b01efda350eae84d2e62db64951c88eb11f11618c9ddbcc15d901c1bf04-init/diff:/var/lib/docker/overlay2/1733638da3e6fec3ec26c4e690875c4e2dedcc1c18af24645ac65171a738bb4a/diff",
                "MergedDir": "/var/lib/docker/overlay2/95b98b01efda350eae84d2e62db64951c88eb11f11618c9ddbcc15d901c1bf04/merged",
                "UpperDir": "/var/lib/docker/overlay2/95b98b01efda350eae84d2e62db64951c88eb11f11618c9ddbcc15d901c1bf04/diff",
                "WorkDir": "/var/lib/docker/overlay2/95b98b01efda350eae84d2e62db64951c88eb11f11618c9ddbcc15d901c1bf04/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "bafdbd85ecbf",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo 123456;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200114",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS",
                "org.opencontainers.image.created": "2020-01-14 00:00:00-08:00",
                "org.opencontainers.image.licenses": "GPL-2.0-only",
                "org.opencontainers.image.title": "CentOS Base Image",
                "org.opencontainers.image.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "d39a6d1e65c4f6c47b731e7ebd9a78db9464729372f7b6eac76da6316439a602",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/d39a6d1e65c4",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "c48cec300a3e4867cfcd296ed0c8458bb647ebedf795d8e22f8e8a82e4190a4b",
            "Gateway": "192.168.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "192.168.0.2",
            "IPPrefixLen": 20,
            "IPv6Gateway": "",
            "MacAddress": "02:42:c0:a8:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "b524243d697c435ea619d54c7eb76edaaef830da15e243576359c3d37792e74f",
                    "EndpointID": "c48cec300a3e4867cfcd296ed0c8458bb647ebedf795d8e22f8e8a82e4190a4b",
                    "Gateway": "192.168.0.1",
                    "IPAddress": "192.168.0.2",
                    "IPPrefixLen": 20,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:c0:a8:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

#### 进入当前运行的容器

```bash
容器通常是使用后台运行的，所以要进入容器
# 方式一 进入容器后开启一个新的终端
docker exec -it 容器id /bin/bash
[root@iZ284pvwwnfZ ~]# docker exec -ti baf /bin/bash
[root@bafdbd85ecbf /]# exit
# 方式二 正在执行当前的命令行
docker attach 容器id
```

#### 从容器内文件拷贝到容器外

```bash
docker cp 容器id:容器内路径 主机内路径
[root@iZ284pvwwnfZ ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
8188d84c619c        centos              "/bin/bash"         2 minutes ago       Up 3 seconds                            boring_mcnulty
[root@iZ284pvwwnfZ ~]# docker cp 8188:/hello.java ./
[root@iZ284pvwwnfZ ~]# ls
hello.java

# 还可以使用数据卷的功能，容器内和主机同步
```

### Docker 小结

![img](https://img2020.cnblogs.com/blog/2007691/202005/2007691-20200528150109512-212016618.png)

## 运行案例

### Nginx 部署

```bash
# 搜索nginx
docker search nginx
# 拉取镜像
docker pull nginx
# 后台运行
# -p:外网端口:容器端口
docker run -d --name nginx01 -p:8080:80 nginx
# 测试
[root@iZ284pvwwnfZ ~]# curl localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
# 进入容器
[root@iZ284pvwwnfZ ~]# docker exec -it nginx01 /bin/bash
root@09abbecf2b22:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@09abbecf2b22:/# cd /etc/nginx/
root@09abbecf2b22:/etc/nginx# ls
conf.d	fastcgi_params	koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params	uwsgi_params  win-utf
root@09abbecf2b22:/etc/nginx#

# 每次部署nginx 都要进入修改配置文件，非常麻烦，可以使用数据卷技术同步配置文件
```

### Tomcat 部署

```bash
# 搜索拉取镜像
docker pull tomcat:9.0
# 你启动容器之后，--rm 会用完即删除，适合测试使用
docker run -it --rm tomcat:9.0
# 后台启动
docker exec -it tomcat01 /bin/bash
# 外网访问没有问题 官方的镜像不完整。
#  linux命令缺少，没有webapps，阿里云默认是最小的镜像,但是有一个webapps.dist
cp -r webapps.dist/* webapps/
# 访问正常
# 但是使用容器，一般使用数据卷，访问服务器的数据，这样可以同步到容器内了。
```

### ES+kibanan 部署

- es 的目录需要放在安全目录
- es 暴露的端口特别多
- es 特别消耗内存 启动需要 2G 内存,所以要限制内存

```bash
# 启动ES
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
# 查看容器的CPU的状态
docker stats
[root@iZ284pvwwnfZ ~]# docker stats
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
7b2159491d25        elasticsearch       0.13%               339.7MiB / 991.1MiB   34.28%              524B / 942B         0B / 0B             42
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
7b2159491d25        elasticsearch       0.38%               339.7MiB / 991.1MiB   34.28%              524B / 942B         0B / 0B             42
# 测试
[root@iZ284pvwwnfZ ~]# curl localhost:9200
{
  "name" : "7b2159491d25",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "M4f2oDEFSTK8Irg0y8rONw",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
#  限制es内存 使用 -e 环境的配置修改
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
[root@iZ284pvwwnfZ ~]# docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
7b2159491d2580a1532e0c2885d462bf1480088a7208e3ba72a42ad243aea140
[root@iZ284pvwwnfZ ~]# docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                            NAMES
7b2159491d25        elasticsearch:7.6.2   "/usr/local/bin/dock…"   4 seconds ago       Up 4 seconds        0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp   elasticsearch
```

### 可视化

#### portainer (生产环境不会使用，测试玩)

```bash
# 图形化的docker管理工具，提供一个后台面板供我们使用
docker run -d -p 8080:9000 --restart=always -v "/var/run/docker.sock:/var/run/docker.sock" --privileged=true portainer/portainer
# [访问测试](http://139.129.118.165:8080/)
```

- 访问测试
  - 设置密码
  - 选择 local 本地管理
    ![](https://img2020.cnblogs.com/blog/2007691/202005/2007691-20200528173343483-768137687.png)

#### Rancher(CI/CD 使用)

## Docker 镜像

### 获得镜像

-

### 提交自己的镜像

```bash
docker commit 提交一个新的副本
docker commit -m "提交的附加信息" -a "作者" 容器id 目标镜像名: [tag]
```

#### 实战测试

- 启动一个默认的 tomcat
- 默认 tomcat 的 webapps 是没有文件的。
- 自己拷贝进去了基本的文件

```bash
[root@iZ284pvwwnfZ ~]# docker commit -a "dkkevincheng" -m "add commit" fa6 tomcat00:1.0
sha256:aa506d07d01b2d050473d20c0638383497a629c12bdf9020e7f779cf1aa90f15
[root@iZ284pvwwnfZ ~]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
tomcat00              1.0                 aa506d07d01b        5 seconds ago       652MB
tomcat                9.0                 1b6b1fe7261e        11 days ago         647MB
```

## 容器数据卷

- 我们的服务是需要数据的，数据是不需要保存在容器里，如果容器删除，数据就会丢失，我们希望数据持久化，删除容器也不会影响。
- 我们需要 mysql 的数据存储在服务器，而不是在容器。
- 就是服务器和容器文件的同步
- 多个容器之间也是可以共享的
  > 方式一

```bash
# 使用命令挂载
docker run it -v 主机目录:容器内的目录
# 测试
[root@iZ284pvwwnfZ webapp]# docker run -it -v /home/webapp:/home/webapp centos /bin/bash
[root@efb884d9398d /]# cd /home/webapp/
[root@efb884d9398d webapp]# touch hello.java
[root@efb884d9398d webapp]# exit
exit
[root@iZ284pvwwnfZ webapp]# cd /home/webapp/
[root@iZ284pvwwnfZ webapp]# ls
hello.c  hello.java
[root@iZ284pvwwnfZ webapp]#

[root@iZ284pvwwnfZ webapp]# docker inspect efb
[
    {
        "Id": "efb884d9398d68ffa261c026ee220698109f47821afa4059106b373bff365302",
        "Created": "2020-05-28T10:30:30.466632059Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 2211,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-05-28T10:32:30.505420825Z",
            "FinishedAt": "2020-05-28T10:30:47.367977548Z"
        },
        "Image": "sha256:470671670cac686c7cf0081e0b37da2e9f4f768ddc5f6a26102ccd1c6954c1ee",
        "ResolvConfPath": "/var/lib/docker/containers/efb884d9398d68ffa261c026ee220698109f47821afa4059106b373bff365302/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/efb884d9398d68ffa261c026ee220698109f47821afa4059106b373bff365302/hostname",
        "HostsPath": "/var/lib/docker/containers/efb884d9398d68ffa261c026ee220698109f47821afa4059106b373bff365302/hosts",
        "LogPath": "/var/lib/docker/containers/efb884d9398d68ffa261c026ee220698109f47821afa4059106b373bff365302/efb884d9398d68ffa261c026ee220698109f47821afa4059106b373bff365302-json.log",
        "Name": "/elastic_gates",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/home/webapp:/home/webapp"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/94fe52fcc6e3025704c3f087976c58750bb84f5c7fc8b3a04731b8676e68ca5f-init/diff:/var/lib/docker/overlay2/1733638da3e6fec3ec26c4e690875c4e2dedcc1c18af24645ac65171a738bb4a/diff",
                "MergedDir": "/var/lib/docker/overlay2/94fe52fcc6e3025704c3f087976c58750bb84f5c7fc8b3a04731b8676e68ca5f/merged",
                "UpperDir": "/var/lib/docker/overlay2/94fe52fcc6e3025704c3f087976c58750bb84f5c7fc8b3a04731b8676e68ca5f/diff",
                "WorkDir": "/var/lib/docker/overlay2/94fe52fcc6e3025704c3f087976c58750bb84f5c7fc8b3a04731b8676e68ca5f/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [ //挂载
            {
                "Type": "bind",
                "Source": "/home/webapp", //主机内地址
                "Destination": "/home/webapp", // 容器内地址
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "efb884d9398d",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200114",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS",
                "org.opencontainers.image.created": "2020-01-14 00:00:00-08:00",
                "org.opencontainers.image.licenses": "GPL-2.0-only",
                "org.opencontainers.image.title": "CentOS Base Image",
                "org.opencontainers.image.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "2f74eebf56fa3079dff7834d9712fd880cbcf01d65792ea5467701c840d75c26",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/2f74eebf56fa",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "71cbb605bcd2554a575faf75aa2c427fe14bae0a3b253e0c669f3fcf080ac04f",
            "Gateway": "192.168.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "192.168.0.2",
            "IPPrefixLen": 20,
            "IPv6Gateway": "",
            "MacAddress": "02:42:c0:a8:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "b524243d697c435ea619d54c7eb76edaaef830da15e243576359c3d37792e74f",
                    "EndpointID": "71cbb605bcd2554a575faf75aa2c427fe14bae0a3b253e0c669f3fcf080ac04f",
                    "Gateway": "192.168.0.1",
                    "IPAddress": "192.168.0.2",
                    "IPPrefixLen": 20,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:c0:a8:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

### 实战 mysql

- mysql 的持久化问题

```bash
# 运行容器需要做数据挂载，安装mysql 需要配置密码，特别注意
# 启动容器
-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器命名
docker run -d -p 3306:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

# 测试 使用navcat连接

# 假设把容器删除，挂载到本地的数据卷依然存在。

```

### 具名挂载、匿名挂载

```bash
# 匿名卷挂载
# 只是指定了容器内的，没有指定容器外的。
-v 数据卷
docker run -d -P --name nginx02 -v /etc/nginx nginx
# 查看本地所有的数据卷
[root@iZ284pvwwnfZ data]# docker volume ls
DRIVER              VOLUME NAME
local               02e0fd0982adbd5538769252ada427fa905ebde1fc5e8c71e6ec2691b3ce6592
local               4ebf2f05624a1cb2bc14dde9d84a94612a10580a994c9f20fa86a21532cf3c59
local               20f99dbcee115f19b232e25ffd74be8c876b14a05c377f2704785b8b5ebd26d9
local               61ef3699a9d001cb23a20f48a6aa7926305caab529d5f8a5eba9072b4151e244
# 具名挂载
docker run -d -P --name nginx03 -v juming:/etc/nginx nginx
[root@iZ284pvwwnfZ data]# docker run -d -P --name nginx03 -v juming:/etc/nginx nginx
e3b8deaa31974d0f3d4c82f9cd1336e5f12431063a118de5bb366ac872cd85e0
[root@iZ284pvwwnfZ data]# docker volume ls
DRIVER              VOLUME NAME
local               02e0fd0982adbd5538769252ada427fa905ebde1fc5e8c71e6ec2691b3ce6592
local               4ebf2f05624a1cb2bc14dde9d84a94612a10580a994c9f20fa86a21532cf3c59
local               20f99dbcee115f19b232e25ffd74be8c876b14a05c377f2704785b8b5ebd26d9
local               61ef3699a9d001cb23a20f48a6aa7926305caab529d5f8a5eba9072b4151e244
local               juming //具名挂载

# 查看卷的目录
# 所有没有指定目录的卷，都在/var/lib/docker/volumes/****/_data 目录下
[root@iZ284pvwwnfZ data]# docker volume inspect juming
[
    {
        "CreatedAt": "2020-05-28T19:24:52+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming/_data",
        "Name": "juming",
        "Options": null,
        "Scope": "local"
    }
]

[root@iZ284pvwwnfZ volumes]# pwd
/var/lib/docker/volumes
[root@iZ284pvwwnfZ volumes]# ls
02e0fd0982adbd5538769252ada427fa905ebde1fc5e8c71e6ec2691b3ce6592
20f99dbcee115f19b232e25ffd74be8c876b14a05c377f2704785b8b5ebd26d9
4ebf2f05624a1cb2bc14dde9d84a94612a10580a994c9f20fa86a21532cf3c59
61ef3699a9d001cb23a20f48a6aa7926305caab529d5f8a5eba9072b4151e244
juming
```

### 扩展

```bash
# 通过 -v的ro只读和rw可读写 控制容器的权限
# 一旦设置了容器的权限，容器就不能从容器内部的改变了
# 默认是rw，可读写
docker run -d -P --name nginx03 -v juming:/etc/nginx:ro nginx
docker run -d -P --name nginx03 -v juming:/etc/nginx:rw nginx
```

## DockerFile

### 介绍 DockerFile

> 方式二 DockerFile 命令脚本

```bash
# 编写脚本dockerfile01
# -f 指定构建文件
# -t 指定版本信息
docker build -f DockerFile01 -t dkkevincheng/centos:1.0 .
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
 ---> 470671670cac
Step 2/4 : VOLUME ["volume01","volume02"]
 ---> Running in 395c6f893551
Removing intermediate container 395c6f893551
 ---> f553d00d4e1e
Step 3/4 : CMD echo "----end-----"
 ---> Running in f16375c616af
Removing intermediate container f16375c616af
 ---> c40ffc7a37cc
Step 4/4 : CMD /bin/bash
 ---> Running in 264ee23b68bd
Removing intermediate container 264ee23b68bd
 ---> b9afb4aaf045
Successfully built b9afb4aaf045
Successfully tagged dkkevincheng/centos:1.0
[root@iZ284pvwwnfZ docker-test-volume]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
dkkevincheng/centos   1.0                 b9afb4aaf045        7 seconds ago       237MB
tomcat00              1.0                 aa506d07d01b        2 hours ago         652MB
mysql                 5.7                 a4fdfd462add        7 days ago          448MB
# 启动镜像
docker run -it b9a /bin/bash
[root@iZ284pvwwnfZ docker-test-volume]# docker run -it b9a /bin/bash
[root@fe80eb5110f5 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01	volume02
# volume01 volume02 是匿名挂载，在docker的vulume文件夹下有这个文件夹
"Mounts": [
            {
                "Type": "volume",
                "Name": "008ed261548e4da1754eea219568d419a8ae3d06158f3142cfd74b1749ad7d05",
                "Source": "/var/lib/docker/volumes/008ed261548e4da1754eea219568d419a8ae3d06158f3142cfd74b1749ad7d05/_data",
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "42468be318304885a75da078a0bc8c911455b466d0ab989c5504d7b87c34ee85",
                "Source": "/var/lib/docker/volumes/42468be318304885a75da078a0bc8c911455b466d0ab989c5504d7b87c34ee85/_data",
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
]
```

### 数据卷容器

- 两个容器之间文件共享
- 使用--volumes-from 两个容器之间的内容是同步的。

```bash
# 启动三个容器
[root@iZ284pvwwnfZ ~]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
dkkevincheng/centos   1.0                 b9afb4aaf045        37 minutes ago      237MB
tomcat00              1.0                 aa506d07d01b        2 hours ago         652MB
mysql                 5.7                 a4fdfd462add        7 days ago          448MB
tomcat                9.0                 1b6b1fe7261e        12 days ago         647MB
tomcat                latest              1b6b1fe7261e        12 days ago         647MB
nginx                 latest              9beeba249f3e        12 days ago         127MB
elasticsearch         7.6.2               f29a1ee41030        2 months ago        791MB
portainer/portainer   latest              2869fc110bf7        2 months ago        78.6MB
centos                latest              470671670cac        4 months ago        237MB
[root@iZ284pvwwnfZ ~]# docker run -it --name centos01 dkkevincheng/centos:1.0
[root@8abe235324d4 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01	volume02
[root@iZ284pvwwnfZ ~]# docker run -it --name centos02 --volumes-from centos01  dkkevincheng/centos:1.0
[root@abd648c5b947 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01	volume02

[root@iZ284pvwwnfZ ~]# docker attach centos01
[root@8abe235324d4 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01	volume02
[root@8abe235324d4 /]# cd volume01
[root@8abe235324d4 volume01]# ls
[root@8abe235324d4 volume01]# touch hello.java
[root@8abe235324d4 volume01]# ls
hello.java
[root@8abe235324d4 volume01]# read escape sequence
[root@iZ284pvwwnfZ ~]# ls
hello.java
[root@iZ284pvwwnfZ ~]# docker attach centos02
[root@abd648c5b947 /]#
[root@abd648c5b947 /]# cd volume01/
[root@abd648c5b947 volume01]# ls
hello.java
```

- 如果删除 centos01 ，数据丢失，centos02 的数据还在。
- 容器之间的数据是双向拷贝的概念
- 容器之间的配置信息的共享 ，数据卷的生命周期一直持续到没有容器使用为止。

### DockerFile 认识

> 构建步骤

- 编写 dockerfile 文件
- docker build 为一个镜像
- docker run 运行一个镜像
- docker push 发布镜像 DockerHub 阿里云镜像仓库

#### DockerFIle 的构建过程

> 指令
> ![](https://img2020.cnblogs.com/blog/2007691/202005/2007691-20200529055854149-1573559445.png)
> ![](https://img2020.cnblogs.com/blog/2007691/202005/2007691-20200529060635043-1928383007.png)

- 每个关键字必须大写
- 执行从上到下
- # 表示注释
- 每一个指令的执行，都会创建提交一个新的镜像层

![](https://img2020.cnblogs.com/blog/2007691/202005/2007691-20200529060050929-831887453.png)

- dockerfile 是面向开发的，使用镜像，只需要编写 dockerfile 文件
- dockerfile 逐渐成为企业交付的标准
- DockerFile：构建文件，定义构建步骤，源代码。
- DockerImage：通过 DockerFile 构建生成的镜像，可以集成 jar 包、war 包，最终发布和运行产品。
- Docker 容器：容器是提供服务的服务器
- CMD # 指定这个容器启动时候要运行的命令，只有一个会生效，会被替代
- ENTRYPOINT # 可以追加命令
- ONBUILD # 当构建一个被继承的 DockerFIle 时候会使用，相当于生命周期的当构建的时候，需不需要先构建一个基础镜像。
- COPY # 复制命令 类似 ADD 命令，把文件拷贝到镜像中
- ENV # 构建的时候设置环境变量

#### 编写 DockerFile 文件

```bash
FROM centos
MAINTAINER dkkevincheng<dkkevincheng@gmail.com>

# 定义工作目录
ENV MYPATH /usr/local
# 进入工作目录
WORKDIR $MYPATH

# 安装需要的命令
RUN yum install -y vim
RUN yum install net-tools

# 暴漏端口
EXPOSE 80

# 执行一些命令
CMD echo $MYPATH
CMD echo "-------end----------"
CMD /bin/bash
```

#### 构建这个镜像

```bash
docker build -f dockerfile-cnetos -t mycentos:1.0 .
Removing intermediate container f8090693aeb8
 ---> 271aaad5269f
Successfully built 271aaad5269f
Successfully tagged mycentos:1.0
```

#### 测试运行

```bash
# 注意一定要带版本号
docker run -it mycentos:1.0 /bin/bash
```

#### 列出镜像的变更历史

```bash
# docker history 镜像id
[root@iZ284pvwwnfZ dockerFile]# docker history 271
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
271aaad5269f        5 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B
30672f2d10fb        5 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B
b262ac46fac6        5 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B
2ccd38d72054        5 minutes ago       /bin/sh -c #(nop)  EXPOSE 80                    0B
492e95ff8584        5 minutes ago       /bin/sh -c yum install -y net-tools             24MB
f7c9f57311be        6 minutes ago       /bin/sh -c yum install -y vim                   59.8MB
87877cdd0a43        6 minutes ago       /bin/sh -c #(nop) WORKDIR /usr/local            0B
4024be6b4807        6 minutes ago       /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B
5f8698fa5af7        6 minutes ago       /bin/sh -c #(nop)  MAINTAINER dkkevincheng<d…   0B
470671670cac        4 months ago        /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           4 months ago        /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>           4 months ago        /bin/sh -c #(nop) ADD file:aa54047c80ba30064…   237MB
```

#### CMD 和 ENTRYPOINT 的区别

```bash
# 测试CMD
FROM centos
CMD ["ls","-a"]

docker build -f dockerfile-cmd-test -t cmdtest:1.0 .

[root@iZ284pvwwnfZ dockerFile]# docker run 4af
.
..
.dockerenv
bin
dev
etc
home
# 想追加命令
[root@iZ284pvwwnfZ dockerFile]# docker run 4af -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-l\": executable file not found in $PATH": unknown.
[root@iZ284pvwwnfZ dockerFile]# docker run 4af ls -al
total 56
drwxr-xr-x  1 root root 4096 May 28 22:56 .
drwxr-xr-x  1 root root 4096 May 28 22:56 ..
-rwxr-xr-x  1 root root    0 May 28 22:56 .dockerenv
lrwxrwxrwx  1 root root    7 May 11  2019 bin -> usr/bin
```

```bash
# ENTRYPOINT 测试
[root@iZ284pvwwnfZ dockerFile]# cat dockerfile-ep-test
FROM centos
ENTRYPOINT ["ls","-a"]
# 构建
docker build -f dockerfile-ep-test -t myeptest:1.0 .
[root@iZ284pvwwnfZ dockerFile]# docker run be8
.
..
.dockerenv
bin
dev
etc
home
[root@iZ284pvwwnfZ dockerFile]# docker run be8 -l
total 56
drwxr-xr-x  1 root root 4096 May 28 22:59 .
drwxr-xr-x  1 root root 4096 May 28 22:59 ..
-rwxr-xr-x  1 root root    0 May 28 22:59 .dockerenv
lrwxrwxrwx  1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x  5 root root  340 May 28 22:59 dev
drwxr-xr-x  1 root root 4096 May 28 22:59 etc
drwxr-xr-x  2 root root 4096 May 11  2019 home
```

### Tomcat 镜像构建

- 准备镜像文件 tomcat 压缩包 jdk 压缩包
  - [Tomcat 9.0 .tar.gz](https://tomcat.apache.org/download-90.cgi)
  - [jdk-8u251-linux-x64.tar.gz](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
- 编写 dockerfile 文件
- 使用 Dockerfile 名字，程序会自己寻找

```bash
FROM centos
MAINTAINER dkkevincheng<dkkevincheng@gmail.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u251-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.35.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_251
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.35
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.35
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.35/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.35/bin/logs/catalina.out
```

- 构建镜像

```bash
dockerfile-tomcat]# docker build -t mytomcat .
```

- 启动镜像

```bash
docker run  -d -p 8080:8080 --name mytom -v /home/ceshi/build/tomcat/test:/usr/local/apache-tomcat-9.0.35/webapps/test -v /home/ceshi/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.35/logs mytomcat
```

- 访问测试

```java
# 新建 WEB-INF/web.xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://java.sun.com/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                               http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
           version="2.5">

  </web-app>
```

```html
# 新建 index.html
<h1>hello docker!</h1>
```

- 发布项目（由于做了卷挂载，在本地编写项目就可以发布了）
- 需要注册自己的账号

### 发送镜像到 dockerhub

```bash
# 登陆账户
docker login
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
Login Succeeded
# 推送自己镜像
# 修改本地镜像版本号
[root@iZ284pvwwnfZ ~]# docker tag 6f9 bartoncheng/mytomcat:1.0
[root@iZ284pvwwnfZ ~]# docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
bartoncheng/mytomcat   1.0                 6f9563ac6966        39 minutes ago      718MB
mytomcat               latest              6f9563ac6966        39 minutes ago      718MB
# 提交到仓库
docker push bartoncheng/mytomcat:1.0
```

### 发布到阿里云上

```bash
$ sudo docker login --username=2020851948@qq.com registry.cn-qingdao.aliyuncs.com
$ sudo docker tag [ImageId] registry.cn-qingdao.aliyuncs.com/bartoncheng/bartoncheng-ceshi:[镜像版本号]
$ sudo docker push registry.cn-qingdao.aliyuncs.com/bartoncheng/bartoncheng-ceshi:[镜像版本号]
```

### 小结

![](https://img2020.cnblogs.com/blog/2007691/202005/2007691-20200529091832208-427429881.png)

## Docker 网络

- 开始之前清空测试环境

```bash
docker rmi -f $(docker images -aq)
```

- ip addr 查看网卡

```bash
[root@iZ284pvwwnfZ ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo # 本地回环地址
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:16:3e:06:05:d3 brd ff:ff:ff:ff:ff:ff
    inet 10.30.105.61/22 brd 10.30.107.255 scope global eth0 # 内网地址
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:16:3e:05:12:d6 brd ff:ff:ff:ff:ff:ff
    inet 139.129.118.165/22 brd 139.129.119.255 scope global eth1 # 公网ip地址
       valid_lft forever preferred_lft forever
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:d3:60:be:dd brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.1/20 brd 192.168.15.255 scope global docker0 # docker的网卡地址
       valid_lft forever preferred_lft forever
```

### docker 如何处理网络访问

- docker 的内网地址进行处理

#### 启动第一个 tomcat

```bash
# 测试网络

docker run -d -P --name tomcat01 tomcat
# 查看第一个网络
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo //容器本机的回环地址
       valid_lft forever preferred_lft forever
95: eth0@if96: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:c0:a8:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.0.2/20 brd 192.168.15.255 scope global eth0 //容器的ip地址
       valid_lft forever preferred_lft forever
```

- eth0@if96 是 docker 的 ip 地址

```bash
# 使用服务器 ping 容器内
[root@iZ284pvwwnfZ ~]#  ping 192.168.0.2
PING 192.168.0.2 (192.168.0.2) 56(84) bytes of data.
64 bytes from 192.168.0.2: icmp_seq=1 ttl=64 time=0.069 ms
64 bytes from 192.168.0.2: icmp_seq=2 ttl=64 time=0.058 ms
64 bytes from 192.168.0.2: icmp_seq=3 ttl=64 time=0.065 ms
```

- 服务器可以 ping 通服务器内部
- 每一次启动 dokcer 容器，docker 就分配一个局域网地址 使用的技术是 evth-pair 技术

```bash
[root@iZ284pvwwnfZ ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:16:3e:06:05:d3 brd ff:ff:ff:ff:ff:ff
    inet 10.30.105.61/22 brd 10.30.107.255 scope global eth0
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:16:3e:05:12:d6 brd ff:ff:ff:ff:ff:ff
    inet 139.129.118.165/22 brd 139.129.119.255 scope global eth1
       valid_lft forever preferred_lft forever
4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:d3:60:be:dd brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.1/20 brd 192.168.15.255 scope global docker0
       valid_lft forever preferred_lft forever
96: veth611cc59@if95: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
# 这个就是docker分配出来的地址
```

#### 启动第二个测试

```bash
# tomcat01
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat01 /bin/bash
root@80291f27483d:/usr/local/tomcat# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
95: eth0@if96: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:c0:a8:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.0.2/20 brd 192.168.15.255 scope global eth0
       valid_lft forever preferred_lft forever
# tomcat02
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat02 /bin/bash
root@510b718d2966:/usr/local/tomcat# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
97: eth0@if98: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:c0:a8:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.0.3/20 brd 192.168.15.255 scope global eth0
       valid_lft forever preferred_lft forever
# 服务器
96: veth611cc59@if95: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 6a:21:5c:c5:97:0a brd ff:ff:ff:ff:ff:ff link-netnsid 0
98: veth8deb3b9@if97: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 12:e9:33:3c:bb:a4 brd ff:ff:ff:ff:ff:ff link-netnsid 1
```

- evth-pair 技术分配都是成对出现的
- 使用 evth-pair 作为桥梁，使用 docker 的协议进行容器之间通信

#### 使用容器之间通信

```bash
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat01 /bin/bash
root@80291f27483d:/usr/local/tomcat# ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.077 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.071 ms
64 bytes from 192.168.0.3: icmp_seq=3 ttl=64 time=0.072 ms
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat02 /bin/bash
root@510b718d2966:/usr/local/tomcat# ping 192.168.0.2
PING 192.168.0.2 (192.168.0.2) 56(84) bytes of data.
64 bytes from 192.168.0.2: icmp_seq=1 ttl=64 time=0.062 ms
64 bytes from 192.168.0.2: icmp_seq=2 ttl=64 time=0.069 ms
64 bytes from 192.168.0.2: icmp_seq=3 ttl=64 time=0.072 ms
```

- docker 容器之间的端口都是虚拟的。
- 容器之间通信没问题
  ![](https://img2020.cnblogs.com/blog/2007691/202005/2007691-20200529102255871-1654859437.png)

#### docker 通过容器名字访问服务

```bash
--link
# 直接测试
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat02 /bin/bash
root@510b718d2966:/usr/local/tomcat# ping tomcat01
ping: tomcat01: Name or service not known

# 启动 tomcat03
# tomcat02 ping tomcat03
[root@iZ284pvwwnfZ ~]# docker run -d -P --name tomcat03 --link tomcat02 tomcat
4eb4d6e88fd99bc61f55bb35b8a6b4ec79433b61eb4499e15c3bf062706b7d7a
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat02 /bin/bash
root@510b718d2966:/usr/local/tomcat# ping tomcat03
ping: tomcat03: Name or service not known
# 不通
# tomcat03 ping tomcat02
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat03 /bin/bash
root@4eb4d6e88fd9:/usr/local/tomcat# ping tomcat02
PING tomcat02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (192.168.0.3): icmp_seq=1 ttl=64 time=0.090 ms
64 bytes from tomcat02 (192.168.0.3): icmp_seq=2 ttl=64 time=0.072 ms
64 bytes from tomcat02 (192.168.0.3): icmp_seq=3 ttl=64 time=0.080 ms
```

#### Docker network

```bash
docker network --help
[root@iZ284pvwwnfZ ~]# docker network list
NETWORK ID          NAME                DRIVER              SCOPE
b524243d697c        bridge              bridge              local
3343c8e69785        host                host                local
8fdfaf0a2dcb        none                null                local
# 查看tomcat03 host
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
192.168.0.3	tomcat02 510b718d2966
192.168.0.4	4eb4d6e88fd9

[root@iZ284pvwwnfZ ~]# docker exec -it tomcat02 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
192.168.0.3	510b718d2966
```

- 本质上 --link 就是 host 绑定，现在不再使用这种方式了
- 我们需要使用自定义网络 network 配置

### 自定义网络

#### 容器互联

```bash
# 查看本地网络
[root@iZ284pvwwnfZ ~]# docker network list
NETWORK ID          NAME                DRIVER              SCOPE
b524243d697c        bridge              bridge              local
3343c8e69785        host                host                local
8fdfaf0a2dcb        none                null                local
```

#### 网络模式

- 桥接 bridge （默认）
- 不配置网络 none
- 主机模式（共享网络）host

#### 自定义网络

- 默认启动的是桥接的 bridge

```bash
[root@iZ284pvwwnfZ ~]# docker network list
NETWORK ID          NAME                DRIVER              SCOPE
b524243d697c        bridge              bridge              local
3343c8e69785        host                host                local
8fdfaf0a2dcb        none                null                local
# docker run -d -P --name morenqidong --net bridge tomcat
# docker run -d -P --name morenqidong  tomcat
# 上面的两个创建方式一样的

# 创建一个自己的网络
docker network create --driver bridge --subnet 172.168.0.0/16 --gateway 172.168.0.1 mynet
# --driver 桥接
# --subnet 子网
# --gateway 网关
[root@iZ284pvwwnfZ ~]# docker network list
NETWORK ID          NAME                DRIVER              SCOPE
b524243d697c        bridge              bridge              local
3343c8e69785        host                host                local
d9d548eff4fa        mynet               bridge              local
8fdfaf0a2dcb        none                null                local

# 创建自己的网络
[root@iZ284pvwwnfZ ~]# docker run -d -P --name tomcat01 --net mynet tomcat
7f07f2b2b221cc9745619d7a88c7bf437bc4fdb5f1268799dcf90268c21ccb06
[root@iZ284pvwwnfZ ~]# docker run -d -P --name tomcat02 --net mynet tomcat
d998ccaa4f44355ad6f52e82ba77475f510e64a6ac53fe980c9dfb5a8155b7ed
[root@iZ284pvwwnfZ ~]# docker inspect mynet
[
    {
        "Name": "mynet",
        "Id": "d9d548eff4fab1982cd3cdc9b344d5a31eafb2062b30a5bb9b5ce5634fe97487",
        "Created": "2020-05-29T11:22:44.557098869+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.168.0.0/16",
                    "Gateway": "172.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "7f07f2b2b221cc9745619d7a88c7bf437bc4fdb5f1268799dcf90268c21ccb06": {
                "Name": "tomcat01",
                "EndpointID": "73574e726902564227018e03735fec5b1183622ffebc3e750fd1143ff8ee2f10",
                "MacAddress": "02:42:ac:a8:00:02",
                "IPv4Address": "172.168.0.2/16",
                "IPv6Address": ""
            },
            "d998ccaa4f44355ad6f52e82ba77475f510e64a6ac53fe980c9dfb5a8155b7ed": {
                "Name": "tomcat02",
                "EndpointID": "47f3701062bf3a440febd5069d8e8d25a6d4dd4726d9feb4a384527516b538f7",
                "MacAddress": "02:42:ac:a8:00:03",
                "IPv4Address": "172.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
# ping 自己的容器
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat02 ping 172.168.0.2
PING 172.168.0.2 (172.168.0.2) 56(84) bytes of data.
64 bytes from 172.168.0.2: icmp_seq=1 ttl=64 time=0.107 ms
64 bytes from 172.168.0.2: icmp_seq=2 ttl=64 time=0.106 ms
64 bytes from 172.168.0.2: icmp_seq=3 ttl=64 time=0.092 ms
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat01 ping tomcat02
PING tomcat02 (172.168.0.3) 56(84) bytes of data.
64 bytes from tomcat02.mynet (172.168.0.3): icmp_seq=1 ttl=64 time=0.052 ms
64 bytes from tomcat02.mynet (172.168.0.3): icmp_seq=2 ttl=64 time=0.087 ms

[root@iZ284pvwwnfZ ~]# docker exec -it tomcat01 ping 172.168.0.3
PING 172.168.0.3 (172.168.0.3) 56(84) bytes of data.
64 bytes from 172.168.0.3: icmp_seq=1 ttl=64 time=0.102 ms
64 bytes from 172.168.0.3: icmp_seq=2 ttl=64 time=0.074 ms
64 bytes from 172.168.0.3: icmp_seq=3 ttl=64 time=0.071 ms
64 bytes from 172.168.0.3: icmp_seq=4 ttl=64 time=0.071 ms
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat02 ping tomcat01
PING tomcat01 (172.168.0.2) 56(84) bytes of data.
64 bytes from tomcat01.mynet (172.168.0.2): icmp_seq=1 ttl=64 time=0.053 ms
64 bytes from tomcat01.mynet (172.168.0.2): icmp_seq=2 ttl=64 time=0.074 ms
```

- 好处是不同的集群使用不同的网络，例如 myslq-网络 和 redis-网络，互相之间隔离的，可以保证安全

#### 网络联通

- 使用 docker network connect
- 一个容器两个 ip 地址

![](https://img2020.cnblogs.com/blog/2007691/202005/2007691-20200529114401781-1938094243.png)

```bash
# 新建一个容器 在默认网段
[root@iZ284pvwwnfZ ~]# docker run -d -P --name tomcat03  tomcat
12108e4c3ab14be06f98d08de5760409fce29756b0c74e40e627dddf101274b8
# 把容器加入到mynet网络
docker network connect 网络名 容器名
docker network connect mynet tomcat03
# 查看mynet配置
[root@iZ284pvwwnfZ ~]# docker inspect mynet
[
    {
        "Name": "mynet",
        "Id": "d9d548eff4fab1982cd3cdc9b344d5a31eafb2062b30a5bb9b5ce5634fe97487",
        "Created": "2020-05-29T11:22:44.557098869+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.168.0.0/16",
                    "Gateway": "172.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "12108e4c3ab14be06f98d08de5760409fce29756b0c74e40e627dddf101274b8": {
                "Name": "tomcat03",
                "EndpointID": "f827ebe0ff9b175197f753d4bb73f6d7805b057c369c545c9986fbd4683d1e98",
                "MacAddress": "02:42:ac:a8:00:04",
                "IPv4Address": "172.168.0.4/16",
                "IPv6Address": ""
            },
            "7f07f2b2b221cc9745619d7a88c7bf437bc4fdb5f1268799dcf90268c21ccb06": {
                "Name": "tomcat01",
                "EndpointID": "73574e726902564227018e03735fec5b1183622ffebc3e750fd1143ff8ee2f10",
                "MacAddress": "02:42:ac:a8:00:02",
                "IPv4Address": "172.168.0.2/16",
                "IPv6Address": ""
            },
            "d998ccaa4f44355ad6f52e82ba77475f510e64a6ac53fe980c9dfb5a8155b7ed": {
                "Name": "tomcat02",
                "EndpointID": "47f3701062bf3a440febd5069d8e8d25a6d4dd4726d9feb4a384527516b538f7",
                "MacAddress": "02:42:ac:a8:00:03",
                "IPv4Address": "172.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
# 测试tomcat03 ping tomcat02
[root@iZ284pvwwnfZ ~]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.168.0.3) 56(84) bytes of data.
64 bytes from tomcat02.mynet (172.168.0.3): icmp_seq=1 ttl=64 time=0.080 ms
64 bytes from tomcat02.mynet (172.168.0.3): icmp_seq=2 ttl=64 time=0.070 ms
64 bytes from tomcat02.mynet (172.168.0.3): icmp_seq=3 ttl=64 time=0.076 ms
```

### 部署 redis 集群

- 分片部署 + 高可用 + 负载均衡

#### 脚本一键创建

```bash
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >> /mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 171.168.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

# 脚本启动
for port in $(seq 1 6); \
do \
docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis-net --ip 171.168.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
done
```

```bash
# 运行
docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
-v /mydata/redis/node-1/data:/data \
-v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis-net --ip 171.168.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6372:6379 -p 16372:16379 --name redis-2 \
-v /mydata/redis/node-2/data:/data \
-v /mydata/redis/node-2/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis-net --ip 171.168.0.12 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6373:6379 -p 16373:16379 --name redis-3 \
-v /mydata/redis/node-3/data:/data \
-v /mydata/redis/node-3/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis-net --ip 171.168.0.13 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6374:6379 -p 16374:16379 --name redis-4 \
-v /mydata/redis/node-4/data:/data \
-v /mydata/redis/node-4/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis-net --ip 171.168.0.14 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
```

#### 创建集群

```bash
[root@iZ284pvwwnfZ ~]# docker exec -it redis-1 /bin/sh
/data # redis-cli --cluster create 171.168.0.11:6379 171.168.0.12:6379 171.168.0.13:6379 171.168.0.14:6379 171
.168.0.15:6379 171.168.0.16:6379 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 171.168.0.15:6379 to 171.168.0.11:6379
Adding replica 171.168.0.16:6379 to 171.168.0.12:6379
Adding replica 171.168.0.14:6379 to 171.168.0.13:6379
M: 9a95a578bd0c7b4a34266772f352d8fbbc7b6da8 171.168.0.11:6379
   slots:[0-5460] (5461 slots) master
M: 4227c4fbb296c40276964cd443589216e48d3d77 171.168.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: a47bbbbaf47e9be13554aa6f69be81ae28142355 171.168.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: 54e4a9d5f91e57b7b505c4587d1180f370a5b572 171.168.0.14:6379
   replicates a47bbbbaf47e9be13554aa6f69be81ae28142355
S: 6662cdcd7f0480cf04bbe6720b1f22830687a3f5 171.168.0.15:6379
   replicates 9a95a578bd0c7b4a34266772f352d8fbbc7b6da8
S: def3a22b2f9bb1aeea84264b164a132bcf1ddf2f 171.168.0.16:6379
   replicates 4227c4fbb296c40276964cd443589216e48d3d77
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
..
>>> Performing Cluster Check (using node 171.168.0.11:6379)
M: 9a95a578bd0c7b4a34266772f352d8fbbc7b6da8 171.168.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 4227c4fbb296c40276964cd443589216e48d3d77 171.168.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 54e4a9d5f91e57b7b505c4587d1180f370a5b572 171.168.0.14:6379
   slots: (0 slots) slave
   replicates a47bbbbaf47e9be13554aa6f69be81ae28142355
S: 6662cdcd7f0480cf04bbe6720b1f22830687a3f5 171.168.0.15:6379
   slots: (0 slots) slave
   replicates 9a95a578bd0c7b4a34266772f352d8fbbc7b6da8
S: def3a22b2f9bb1aeea84264b164a132bcf1ddf2f 171.168.0.16:6379
   slots: (0 slots) slave
   replicates 4227c4fbb296c40276964cd443589216e48d3d77
M: a47bbbbaf47e9be13554aa6f69be81ae28142355 171.168.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

#### 连接集群

```bash
/data # redis-cli -c
127.0.0.1:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:213
cluster_stats_messages_pong_sent:225
cluster_stats_messages_sent:438
cluster_stats_messages_ping_received:220
cluster_stats_messages_pong_received:213
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:438
127.0.0.1:6379> cluster nodes
4227c4fbb296c40276964cd443589216e48d3d77 171.168.0.12:6379@16379 master - 0 1590725846000 2 connected 5461-10922
9a95a578bd0c7b4a34266772f352d8fbbc7b6da8 171.168.0.11:6379@16379 myself,master - 0 1590725845000 1 connected 0-5460
54e4a9d5f91e57b7b505c4587d1180f370a5b572 171.168.0.14:6379@16379 slave a47bbbbaf47e9be13554aa6f69be81ae28142355 0 1590725845000 4 connected
6662cdcd7f0480cf04bbe6720b1f22830687a3f5 171.168.0.15:6379@16379 slave 9a95a578bd0c7b4a34266772f352d8fbbc7b6da8 0 1590725847009 5 connected
def3a22b2f9bb1aeea84264b164a132bcf1ddf2f 171.168.0.16:6379@16379 slave 4227c4fbb296c40276964cd443589216e48d3d77 0 1590725846407 6 connected
a47bbbbaf47e9be13554aa6f69be81ae28142355 171.168.0.13:6379@16379 master - 0 1590725846000 3 connected 10923-16383
127.0.0.1:6379> set a b
-> Redirected to slot [15495] located at 171.168.0.13:6379
OK
```

#### 停止 redis-3

```bash
# 停止
docker stop redis-3
# 测试
171.168.0.13:6379> get a
# 这里需要重新链接一下
^C
/data # redis-cli -c
127.0.0.1:6379> get a
-> Redirected to slot [15495] located at 171.168.0.14:6379
"b"
171.168.0.14:6379> cluster nodes
54e4a9d5f91e57b7b505c4587d1180f370a5b572 171.168.0.14:6379@16379 myself,master - 0 1590726106000 7 connected 10923-16383
9a95a578bd0c7b4a34266772f352d8fbbc7b6da8 171.168.0.11:6379@16379 master - 0 1590726107090 1 connected 0-5460
6662cdcd7f0480cf04bbe6720b1f22830687a3f5 171.168.0.15:6379@16379 slave 9a95a578bd0c7b4a34266772f352d8fbbc7b6da8 0 1590726107692 5 connected
def3a22b2f9bb1aeea84264b164a132bcf1ddf2f 171.168.0.16:6379@16379 slave 4227c4fbb296c40276964cd443589216e48d3d77 0 1590726107000 6 connected
a47bbbbaf47e9be13554aa6f69be81ae28142355 171.168.0.13:6379@16379 master,fail - 1590726020393 1590726019000 3 connected
4227c4fbb296c40276964cd443589216e48d3d77 171.168.0.12:6379@16379 master - 0 1590726107591 2 connected 5461-10922
171.168.0.14:6379>
```

## Docker Compose

- docker compose 是容器编排工具，可以批量控制容器
- [安装地址](https://docs.docker.com/compose/install/)

#### 安装 docker compose

```bash
# 安装compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
# 授权
sudo chmod +x /usr/local/bin/docker-compose
# 测试
docker-compose --version
docker-compose -v
# 结果
[root@iZ284pvwwnfZ ~]# docker-compose --version
docker-compose version 1.25.5, build 8a1c60f6
```

## Docker Swarm

## CI/CD 持续部署 Jenkins

## Docker + k8s

### 微服务架构

- 传统的架构
  ![](https://img2020.cnblogs.com/blog/2007691/202006/2007691-20200604112405210-429293253.png)
- 缺点
  - 内存消耗高
  - 架构昂贵
  - 伸缩性不强
- 微服务架构
  ![](https://img2020.cnblogs.com/blog/2007691/202006/2007691-20200604112421907-1656121024.png)
- 业务拆分需要增加工作量 DDD(领域驱动设计)
  - 拆分的太小，会浪费资源
  - 拆分太大，不能发挥微服务的特性
- 数据的一致性
- 微服务 API 改变，沟通成本增加

### 带来的问题和解决方案

- 微服务之间如何通讯？
  - 一对一或者是一对多
  - 同步的还是异步的
- 微服务是如何发现彼此的？
  - 服务之间的 Rest API
  - RPC 一般使用这种通讯
  - 消息队列
- 微服务如何部署、更新和扩容？
