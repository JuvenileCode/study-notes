## Docker 暇记

### Docker简介

#### Docker是什么

Docker 是一个应用打包、分发、部署的工具；可以把它理解为一个轻量的虚拟机，它只虚拟你软件需要的运行环境，相对普通虚拟机占用更少的资源。

#### 重要概念：镜像、容器

**镜像**：可以理解为软件安装包，可以方便的进行传播和安装。
**容器**：软件安装后的状态，每个软件运行环境都是独立的、隔离的，称之为容器。

### Docker安装软件

Docker 官方镜像仓库查找 需要安装的软件：https://hub.docker.com/

[官方命令查找](https://docs.docker.com/engine/reference/commandline/run/)

#### Redis安装

```bash
# 一个命令跑起来
docker run -d -p 6379:6379 --name redis redis:latest
```

**命令解释**

```bash
docker run # docker run相当于 /createthen 和 /start 命令结合 （新建容器并允许）
-d # 以后台方式运行
-p 6379:6379 # 把容器内部6379端口暴露到外部6379端口
--name redis redis:latest # 指定redis容器名使用redis:latest容器
```

**自定义配置文件**

1. 创建配置文件目录存放redis.conf，文件从[官网下载](https://links.jianshu.com/go?to=http%3A%2F%2Fdownload.redis.io%2Fredis-stable%2Fredis.conf)，修改所需配置
2. 启动命令，[参考](https://segmentfault.com/a/1190000039769819)

```bash
docker run -p 6379:6379 --name redis -v /usr/local/docker/redis.conf:/etc/redis/redis.conf -v /usr/local/docker/data:/data -d redis redis-server /etc/redis/redis.conf --appendonly yes
# -v 挂载目录，规则与端口映射相同
```

#### Nginx安装

```bash
# 拉取镜像
docker pull nginx
# 启动
docker run -d --name nginx -p 80:80 [镜像ID]
# 或 docker run -d -p 8080:80 --name nginx nginx
```

**本地项目用Nginx访问：**

```shell
docker run -d -p 8080:80 --name nginx -v D:\WebStorm\project\react-stagging\build:/usr/share/nginx/html nginx
```

#### Mysql安装

```bash
# 拉去最新镜像
docker pull mysql
# 启动
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
# 创建挂载目录
mkdir -p /usr/local/mysql/conf
mkdir -p /usr/local/mysql/data
mkdir -p /usr/local/mysql/logs
# 挂载目录后启动
docker run -d -p 3306:3306 --name mysql -v /usr/local/mysql/conf/my.cnf:/etc/mysql/my.cnf -v /usr/local/mysql/logs:/logs -v /usr/local/mysql/data/mysql:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=123456 mysql
```

### 目录挂载

```shell
# 查看所有挂载
docker volume ls
# 具体查看某一个挂载
docker volume inspect 卷名
```

#### bind mount

`bind mount` 直接把宿主机目录映射到容器内，适合挂代码目录和配置文件。可挂到多个容器上

```shell
# -v 目录挂载  冒号前为 外部目录，冒号后为 容器内目录；相当于外部目录中的内容会映射同步到容器内
docker run -d -P -v /usr/local/nginx/has-name-nginx:/etc/nginx --name nginx01 nginx
```

#### volume

`volume` 由容器创建和管理，创建在宿主机，所以删除容器不会丢失，官方推荐，更高效，Linux 文件系统，适合存储数据库数据。可挂到多个容器上

```shell
# 具名挂载数据卷
docker run -d -P -v has-name-nginx:/etc/nginx --name nginx01 nginx

# 查看某个具名挂载卷的详细信息
docker volume inspect has-name-nginx
[
    {
        "Mountpoint": "/var/lib/docker/volumes/has-name-nginx/_data"
    }
]
```

#### tmpfs ount

`tmpfs mount` 适合存储临时文件，存宿主机内存中。不可多容器共享



### 常用命令

[命令参考链接](https://cloud.tencent.com/developer/article/1772136)

#### 服务

```bash
# 查看Docker信息
docker info
# 查看Docker版本信息
docker version
# 查看docker简要信息
docker -v
# 启动Docker
systemctl start docker
# 关闭docker
systemctl stop docker
# 重启docker服务
service docker restart
# 关闭docker服务
service docker stop
```

#### 镜像

```bash
# 检索镜像
docker search 关键字Nginx
# 列出镜像
docker image ls
docker images
# 删除镜像
docker rmi <镜像Id>
# 导出镜像
docker save
# 导入镜像
docker load
```

#### 容器

```bash
# 新建并启动
docker run [镜像名/镜像ID]
# 启动已终止容器
docker start [容器ID]
# 查看容器详细信息
docker inspect [容器ID]

# 列出本机运行的容器
$ docker ps 
# 列出本机所有的容器（包括停止和运行）
$ docker ps -a

# 停止运行的容器
docker stop [容器ID]
# 杀死容器进程
docker  kill [容器ID] 

# 重启容器
docker restart [容器ID] 
# 删除容器
docker  rm [容器ID]

# 导出一个已经创建的容器到一个文件
docker export [容器ID]
# 导入的容器快照文件可以再导入为镜像
docker import [路径]
```

**进入容器**

```bash
# 如果从这个 stdin 中 exit，会导致容器的停止
docker attach [容器ID]
# 交互式进入容器(常用)
docker exec -i -t  [容器ID] /bin/bash
```

进入容器通常使用第二种方式，`docker exec`后面跟的常见参数如下：

- **-d :**分离模式: 在后台运行
- **-i :**即使没有附加也保持STDIN 打开
- **-t :**分配一个伪终端

**查看日志**

```bash
docker logs -f [容器ID]
```

-f : 跟踪日志输出

--since :显示某个开始时间的所有日志

-t : 显示时间戳

--tail :仅列出最新N条容器日志

#### Dockerfile

```bash
# 常见指令
 FROM：指定基础镜像
 RUN：执行命令
 COPY：复制文件
 ADD：更高级的复制文件
 CMD：容器启动命令
 ENV：设置环境变量
 EXPOSE：暴露端口
 
# 镜像构建 -t 设置镜像名字和版本号
docker build -t testName:v1Version
# 镜像运行
docker run -p 8080:8080 --name test-hello testName:v1Version
```

### 网络通信

[参考资料一](https://www.cnblogs.com/mrhelloworld/p/docker11.html)

#### bridge

**bridge模式，--net=bridge(默认)**，为每一个容器分配、设置 IP 等，并将容器连接到一个 `docker0` 虚拟网桥，默认为该模式。

#### host

**host模式，--net=host**，这个模式下创建出来的容器，直接使用容器宿主机的网络命名空间。将不拥有自己独立的Network Namespace，即没有独立的网络环境。它使用宿主机的ip和端口。

#### none

**none模式，--net=none**，容器有独立的 Network namespace，但并没有对其进行任何网络设置，如分配 veth pair 和网桥连接，IP 等。

#### container

新创建的容器不会创建自己的网卡和配置自己的 IP，而是和一个指定的容器共享 IP、端口范围等。