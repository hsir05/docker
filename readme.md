# docker

[阮一峰--docker](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

## 1. 什么是docker

    Docker 是使用 Go 语言开发的一种 Linux 容器封装，提供简单易用的使用接口，是目前最流行的 Linux 容器解决方案

## 2. docker 用途

+ 提供一致的开发，测试，生产环境
+ 创建隔离的运行环境
+ 组建微服务架构

## 3. docker 安装

1. [mac](https://docs.docker.com/docker-for-mac/install/)
    
   使用 **`docker version` 或 `docker info`** 命令验证`docker`是否安装成功

## 4. docker 启动

```docker
 docke 如果没有启动, 可以用命令启动
 sudo service docker start
```

## 5. docker镜像

    **拉取镜像**
    `docker image pull nginx`

    **查看镜像**
    `docker image ls`

    **删除镜像**
    删除镜像必须是在使用该镜像容器停止的情况下，否则会报错
    `docker image rmi <image ID>`

    **发布镜像文件**
    如果有 `hub.docker.com ` 账号，可以直接登陆 `docker login`, 如果没有，可以去注册个

```docker
docker image tag imageName:0.0.1 user/hello-world:0.0.1

imageName: 镜像名称
user: 用户名
0.0.1: tag
```

**发布**
`docker image push [usernam]/[repository]:[tag]`


## 6. 运行容器

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


**启动容器**

    命令启动停止运行的容器，同理可以根据 容器名或者 容器 id 进行启动
    docker start container-name/container-id


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

**查看容器**
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

**进入容器内部**
`docker exect -it 02277acc3efc bash`

**直接查看容器信息**
`docker inspect 02277acc3efc`

`/etc/hosts 下可以看到容器的ip信息等`


## 7. `Dockerfile` 文件

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

`docker image build -t demo:0.0.1 .`

```zsh
-t 指定镜像的名称
后面添加冒号指定tag, 如果不加，则默认tag是latest
后面的点表示Dockerfile文件所在的位置
```

**创建好后，运行 `docker image ls | grep demo
` ，可以看到镜像已经存在了**

## 8. 容器日志

**使用 `docker logs container-name/container-id` 命令 可以查看容器日志信息，指定容器名或者 容器 id 即可**


## 9. 其他命令

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

## 10. Docker Compose

```
Docker Compose是 docker 提供的一个命令行工具，用来定义和运行由多个容器组成的应用。使用 compose，我们可以通过 YAML 文件声明式的定义应用程序的各个服务，并由单个命令完成应用的创建和启动
```
**查看版本**
`docker-compose --version`

建立一个目录，然后在目录中建立 `docker-compse.yml`, 内容如下，

```zsh
version: "3.7"
services:
    info:
        container_name: demo
        image: user/demo:latest
        ports:
            - "8081:80"
        restart: on-failure
```

使用命令 `docker-compose up info` 就可以将启动起来了

**Volume**

+ 可以将容器内和宿主机的某个文件夹进行”绑定“，任何文件改动都会得到同步。所以，我可以将整个站点目录和 数据库 目录都挂载为 Volume。这样，当容器删除时，所有数据文件和源码都会保留。

```
version: "3.7"
services:
    info:
        container_name: demo
        image: user/demo:latest
        ports:
            - "8081:80"
        restart: on-failure
+   demo:
+       container_name: demo
+       image: tutum/lamp:latest
+       ports:
+           - "8081:80"
+       volumes:
+           - ./demo/mysql-data:/var/lib/mysql
+           - ./demo/demo:/app
+       restart: on-failure

```
