# Docker的启动与停止

**systemctl**命令是系统服务管理器指令

启动docker：

```shell
systemctl start docker 
```

停止docker：

```sheell
systemctl stop docker
```

重启docker：

```shell
systemctl restart docker
```

查看docker状态：

```shell
systemctl status docker
```

开机启动：

```shell
systemctl enable docker
```

查看docker概要信息

```shell
docker info
```

查看docker帮助文档

```shell
docker --help
```



# 常用命令

## 镜像相关命令

### 1. 查看镜像

```shell
docker images [OPTIONS] [REPOSITORY[:TAG]]
```

REPOSITORY：镜像名称

TAG：镜像标签

IMAGE ID：镜像ID

CREATED：镜像创建日期

SIZE：镜像大小

这些镜像都是存储在Docker宿主机的/var/lib/docker目录下

### 2. 搜索镜像

从镜像仓库中查找镜像（默认从Docker Hub）

```shell
docker search 镜像名称
```

NAME：仓库名称
DESCRIPTION：镜像描述

STARS：用户评价，反映一个镜像的受欢迎程度

OFFICIAL：是否官方
AUTOMATED：自动构建，便是该镜像由Docker Hub自动构建流程创建的

### 3. 拉取镜像

从镜像仓库中拉取或者更新指定镜像

 ```shell
docker pull 镜像名称
 ```

### 4. 删除镜像

按镜像ID删除镜像

```shell
docker rmi 镜像ID
```



## 容器相关命令

### 1. 查看容器

查看正在运行的容器

```shell
docker ps
```

查看所有容器

```shell
docker ps -a
```

查看最后一次运行的容器

```shell
docker ps -l
```

查看停止的容器

```shell
docker ps -f status=exited
```

### 2. 创建与启动容器

创建容器命令：docker run

OPTIONS说明：

- **-d:** 会创建一个守护式容器在后台运行（不会自动登录容器，如果只加-i-t两个参数，创建后就会自动进入容器），并返回容器ID；
- **-i:** 以交互模式运行容器，通常与 -t 同时使用；
- **-p:** 指定端口映射，格式为：**主机(宿主)端口:容器端口**，可以使用多个-p做多个端口映射
- **-t:** 为容器重新分配一个伪输入终端，表示容器启动后会进入其命令行，通常与 -i 同时使用；
- **--name="nginx-lb":** 为容器指定一个名称；
- **-v:** 表示目录映射关系，格式为：**主机(宿主)目录:容器目录**，可以使用多个-v做多个目录映射。注意：最好做目录映射，在宿主机上做修改，然后共享到容器上。

（1）交互式方式创建容器

```shell
docker run -it --name=容器名称 镜像名称:标签 /bin/bash
```

退出当前容器

```
exit
```

（2）守护式方式创建容器

```shell
docker run -di --name=容器名称 镜像名称:标签
```

登录守护式创建容器

```shell
docker exec -it 容器名称(容器ID) 镜像名称:标签 /bin/bash
```

### 3. 停止和启动容器

停止容器

```shell
docker stop 容器名称(容器ID)
```

启动容器

``` shell
docker start 容器名称(容器ID)
```

### 4. 文件拷贝

将文件拷贝到容器内部可以使用CP命令

```shell
docker cp 需要拷贝的文件或目录 容器名称:容器目录
```

将文件从容器内部拷贝出来

```shell
docker cp 容器名称:容器目录 需要拷贝的文件或目录
```

### 5. 目录挂载

创建容器的时候，将宿主机的目录与容器内的目录进行映射，这样我们就可以通过修改宿主机某个目录的文件从而去影响容器。

创建容器 添加-v参数 后面为 宿主机目录:容器目录，例如：

```shell
docker run -di -v /usr/local/myhtml:/usr/local/mythml
```

### 6. 查看容器IP地址

查看容器运行的各种数据

```shell
docker inspect 容器名称（容器ID）
```

也可以直接执行下面的命令直接输出IP地址

```shell
docker inspect --format='{{.NetworkSetting.IPAddress}}' 容器名称（容器ID）
```

### 7. 删除容器

删除指定的容器

```shell
docker rm 容器名称（容器ID）
```



# 应用部署

## MySQL部署

（1）拉取mysql镜像

```shell
docker pull mysql
```

（2）创建容器

```shell
docker run -di --name=mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

-p 代表端口映射，格式为宿主机映射端口：容器运行端口

-e 代表添加环境变量 MYSQL_ROOT_PASSWORD 是root用户的登录密码

（3）远程登录mysql

连接宿主机的IP，指定端口为33306

## Tomcat部署

（1）拉取tomcat镜像

```shell
docker pull tomcat
```

（2）创建容器

```shell
docker run -di --name=tomcat -p 8080:8080 tomcat
-v /usr/local/webapps:/usr/local/tomcat/webapps tomcat
```

## Nginx部署

（1）拉取Nginx镜像

```shell
docker pull nginx
```

（2）创建容器

```shell
docker run -di --name=nginx -p 80:80 nginx
```

## Redis部署

1）拉取redis镜像

```shell
docker pull redis
```

（2）创建容器

```shell
docker run -di --name=redis -p 6379:6379 redis
```

# 迁移与备份

## 1. 容器保存为镜像

将容器保存为镜像

```shell
docker commit nginx nginx_i
```

## 2. 镜像备份

将镜像保存为tar文件

```shell
docker save -o nginx.tar nginx_i
```

## 3. 镜像恢复与迁移

要确认不存在要恢复的镜像，然后执行命令恢复

```shell
docker load -i nginx.tar
```

-i 输入的文件

执行后再次查看镜像，可以看到镜像已经恢复

# Dockerfile

### 1. 什么是 Dockerfile？

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

## 2. 常用命令



| 命令                               | 作用                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| FROM images_name:tag               | 定义了使用哪个基础镜像启动构建流程                           |
| MANITAINER user_name               | 声明镜像的创建者                                             |
| ENV key value                      | 设置环境变量（可以写多条）                                   |
| RUN command                        | 是Dockerfile的核心部分（可以写多条）                         |
| ADD source dir/file dest_dir/file  | 将宿主机的文件复制到容器内，如果是一个压缩文件，将会在复制后自动解压 |
| COPY source dir/file dest_dir/file | 和ADD相似，但是如果有压缩文件并不能解压                      |
| WORKDIR path_dir                   | 设置工作目录                                                 |

## 3. 使用脚本创建镜像

步骤：

（1）创建目录  

```shell
mkdir -p /usr/local/dockerjdk8
```

（2）下载jdk-8u221-linux-x64.tar.gz并上传到服务器（虚拟机）中的/usr/local/dockerjdk8目录

（3）创建文件Dockerfile `vi Dockerfile`

```shell
FROM ubuntu:latest
# 作者
MAINTAINER vtkd
# 工作目录
WORKDIR /usr
# 创建一个目录
RUN mkdir /usr/local/java
# ADD是相对路径jar，bajava添加到容器中
ADD jdk-8u221-linux-x64.tar.gz /usr/local/java/

# 配置java环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_221
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/bin/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
ENV PATH $JAVA_HOME/bin:$PATH
```

（4）执行命令构建镜像

```shell
docker build -t='jdk8' .
```

注意后边的空格和点，不要省略

（5）查看镜像是否建立完成

```shell
docker images
```



# Docker 私有仓库

## 1. 私有仓库搭建与配置

（1）拉取私有仓库镜像

```shell
docker pull registry
```

（2）启动私有仓库容器

```shell
docker run -di --name=registry -p 5000:5000 registry
```

（3）打开浏览器输入地址http://192.168.18.133:5000/v2/_catalog看到`{"repositories":[]}`表示私有仓库搭建成功并且内容为空

（4）修改daemon.json

```shell
vi /etc/docker/daemon.json
```

添加以下内容，保存退出。

```shell
"insecure-registries":["192.168.18.133:5000"]
```

用于让docker信任私有仓库地址

（5）重启docker服务

```shell
systemctl restart docker
```

## 2. 镜像上传至私有仓库

（1）标记此镜像为私有仓库的镜像

```shell
docker tag jdk1.8 192.168.18.133:5000/jdk1.8
```

（2）启动私有仓库容器

```shell
docker start registry
```

（3）上传标记的镜像

```shell
docker push 192.168.18.133:5000/jdk1.8
```

## 从私有仓库中拉取镜像

```shell
docker pull 192.168.18.133:5000/nginx
pull：后边跟的是私有仓库的地址
```

