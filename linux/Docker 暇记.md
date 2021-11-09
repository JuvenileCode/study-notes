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



### 常用命令

[命令参考链接](https://cloud.tencent.com/developer/article/1772136)

#### 服务

```bash
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
docker rim <镜像Id>
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
docker exec -i [容器ID]
```

进入容器通常使用第二种方式，`docker exec`后面跟的常见参数如下：

－ d, --detach 在容器中后台执行命令； 

－ i, --interactive=true I false ：打开标准输入接受用户输入命令

**查看日志**

```bash
docker logs -f [容器ID]
```

-f : 跟踪日志输出

--since :显示某个开始时间的所有日志

-t : 显示时间戳

--tail :仅列出最新N条容器日志