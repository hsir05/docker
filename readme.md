# docker

[阮一峰--docker](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

## 1. docker 容器和镜像的删除

### 拉取镜像

```docker
docker image pull nginx

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

