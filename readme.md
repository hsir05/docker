# docker

[阮一峰--docker](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

## docker 用途
+ 提供一次性的环境
+ 提供弹性的云服务
+ 组建微服务i架构

## docker 安装

[mac](https://docs.docker.com/docker-for-mac/install/)

**`docker version` 或 `docker info`** 命令验证`docker`是否安装成功

## docker 启动

```
`docker` 如果没有启动, 可以用一下命令启动
 service 命令用法
 sudo service docker start
 systemctl 命令用法
 sudo systemctl start docker
```

## 1. docker 容器和镜像的删除

### 拉取镜像

```docker
docker image pull nginx

拉取到镜像后可以用如下命令查看到镜像
`docker image ls`

```

### 1.1 运行容器

```docker
docker run --name container-name:tag -d image-name

--name：自定义容器名，不指定时，docker 会自动生成一个名称
-d：表示后台运行容器
image-name：指定运行的镜像名称以及 Tag
```

**端口映射**

```docker
docker run --name container-name:tag -d -p 服务器端口:Docker 端口 image-name

-p 表示进行服务器与 Docker 容器的端口映射，默认情况下容器中镜像占用的端口是 Docker 容器中的端口与外界是隔绝的，必须进行端口映射才能访问
```

**启动成功之后，`ip addr show` 查一下服务器 ip 地址（ 192.168.58.129），然后就能从物理机上访问了**


### 1.2 启动容器

```docker
    命令启动停止运行的容器，同理可以根据 容器名或者 容器 id 进行启动
    docker start container-name/container-id
```

### 1.3 删除,停止容器

    **获取容器的信息**
    `docker container ls -a`

    **获取容器的id**
    `docker container ls -a -p`

    **如果容器是运行状态，必须先把容器停止了**
    `docker container stop <container ID>`

    **找到容器对应名字或id进行删除**
    `docker container rm <container ID>`

    **批量停止容器:**
    `docker container stop $(docker container ls -a -q)`

    **批量删除容器:**
    `docker container rm $(docker container ls -a -q)`
    

### 1.4 删除镜像

**删除镜像的方法和删除容器的方法一样，把`container`换成`image`即可**

**有时候删除镜像会报错，是因为镜像正在被容器使用，停止容器后，移除镜像即可**


### 1.5 查看容器

```docker
docker ps 可以查看运行中的容器
docker ps -a 可以查看所有容器

```

**参数说明**

```docker
CONTAINER ID：容器 di

IMAGE：镜像名称:Tag

 COMMAND：命令

CREATES：容器创建的时刻

STATUS：容器当前的状态 (up 表示运行、Exited 表示停止运行)

PORTS：镜像程序使用的端口号
```

### 1.6 容器日志

**使用 `docker logs container-name/container-id` 命令 可以查看容器日志信息，指定容器名或者 容器 id 即可**


###    `Dockerfile` 文件

**Docker 文件 是一个文本文件，用来配置image， Docker 根据该文件生成二进制的`image` 文件**

```
FROM node:8.4
COPY . /app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
```

**含义**

```docker
FROM node:8.4：该 image 文件继承官方的 node image，冒号表示标签，这里标签是8.4，即8.4版本的 node。
COPY . /app：将当前目录下的所有文件（除了.dockerignore排除的路径），都拷贝进入 image 文件的/app目录。
WORKDIR /app：指定接下来的工作路径为/app。
RUN npm install：在/app目录下，运行npm install命令安装依赖。注意，安装后所有的依赖，都将打包进入 image 文件。
EXPOSE 3000：将容器 3000 端口暴露出来， 允许外部连接这个端口
```

**创建 `Dockerfile` 文件后，就可以创建  `image` 文件了**

`docker image build -t koa-demo:0.0.1 .`

```
-t 指定镜像的名称
后面添加冒号指定tag, 如果不加，则默认tag是latest
后面的点表示Dockerfile文件所在的位置
```

### 发布镜像文件

如果有 `hub.docker.com ` 账号，就可以登陆了

`docker login`

 为本地 `image` 标注用户名和版本

```docker
docker image tag koa-demos:0.0.1 skills/hello-world:0.0.1
```

**发布**

`docker image push [usernam]/[repository]:[tag]`

ok

**其他命令**

```
docker container starat [containerId] 用来启动已经生成，已经停止的容器
```

```
docker container exec 进入一个正在运行的容器， 进入容器后就可以使用shell 执行命令了(如果没有 -it 命令参数)

```

```
docker container cp 命令用于从正在运行的 Docker 容器里面，将文件拷贝到本机

如：
docker container cp [containID]:[/path/to/file] .
```