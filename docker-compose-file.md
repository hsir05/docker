# 模版文件

Compose模板文件是一个定义服务、网络和卷的YAML文件。Compose模板文件默认路径是当前目录下的docker-compose.yml，可以使用.yml或.yaml作为文件扩展名。
Docker-Compose标准模板文件应该包含version、services、networks 三大部分，最关键的是services和networks两个部分

```zsh
version: '2'
services:
  web:
    image: dockercloud/hello-world
    ports:
      - 8080
    networks:
      - front-tier
      - back-tier

  redis:
    image: redis
    links:
      - web
    networks:
      - back-tier

  lb:
    image: dockercloud/haproxy
    ports:
      - 80:80
    links:
      - web
    networks:
      - front-tier
      - back-tier
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock 

networks:
  front-tier:
    driver: bridge
  back-tier:
    driver: bridge
```

## 1. image

image是指定服务的镜像名称或镜像ID。如果镜像在本地不存在，Compose将会尝试拉取镜像

```zsh
services: 
    web: 
        image: hello-world
```

## 2. build

务除了可以基于指定的镜像，还可以基于一份Dockerfile，在使用up启动时执行构建任务，构建标签是build，可以指定Dockerfile所在文件夹的路径。Compose将会利用Dockerfile自动构建镜像，然后使用镜像启动服务容器

`build: /path/to/build/dir`

也可以是相对路径，只要上下文确定就可以读取到Dockerfile。

`build: ./dir`

设定上下文根目录，然后以该目录为准指定Dockerfile。

```zsh
build:
  context: ../
  dockerfile: path/of/Dockerfile
```
build都是一个目录，如果要指定Dockerfile文件需要在build标签的子级标签中使用dockerfile标签指定。
如果同时指定image和build两个标签，那么Compose会构建镜像并且把镜像命名为image值指定的名字

## 3. context

context选项可以是Dockerfile的文件路径，也可以是到链接到git仓库的url，当提供的值是相对路径时，被解析为相对于撰写文件的路径，此目录也是发送到Docker守护进程的context

```zsh
build:
  context: ./dir
```

## 4. dockerfile

使用dockerfile文件来构建，必须指定构建路径

```zsh
build:
  context: .
  dockerfile: Dockerfile-alternate
```

dockerfile指令不能跟image同时使用，否则Compose将不确定根据哪个指令来生成最终的服务镜像

## 5. command

使用command可以覆盖容器启动后默认执行的命令

`command: bundle exec thin -p 3000`

## 6. container_name

Compose的容器名称格式是： <项目名称> <服务名称> <序号>
可以自定义项目名称、服务名称，但如果想完全控制容器的命名，可以使用标签指定

`container_name: app`

## 7. net

```zsh
net: "bridge"
net: "none"
net: "host"
```

## 8. depends_on

在使用Compose时，最大的好处就是少打启动命令，但一般项目容器启动的顺序是有要求的，如果直接从上到下启动容器，必然会因为容器依赖问题而启动失败。例如在没启动数据库容器的时候启动应用容器，应用容器会因为找不到数据库而退出。depends_on标签用于解决容器的依赖、启动先后的问题

```zsh
version: '2'
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```

上述YAML文件定义的容器会先启动redis和db两个服务，最后才启动web 服务

## 9. ports

ports用于映射端口的标签。
使用HOST:CONTAINER格式或者只是指定容器的端口，宿主机会随机映射端口。

```zsh
ports:
 - "3000"
 - "8000:8000"
 - "49100:22"
 - "127.0.0.1:8001:8001"
```

当使用HOST:CONTAINER格式来映射端口时，如果使用的容器端口小于60可能会得到错误得结果，因为YAML将会解析xx:yy这种数字格式为60进制。所以建议采用字符串格式

## 10. volumes

挂载一个目录或者一个已存在的数据卷容器，可以直接使用 [HOST:CONTAINER]格式，或者使用[HOST:CONTAINER:ro]格式，后者对于容器来说，数据卷是只读的，可以有效保护宿主机的文件系统。
Compose的数据卷指定路径可以是相对路径，使用 . 或者 .. 来指定相对目录。
数据卷的格式可以是下面多种形式：

```zsh
volumes:
  // 只是指定一个路径，Docker 会自动在创建一个数据卷（这个路径是容器内部的）。
  - /var/lib/mysql
  // 使用绝对路径挂载数据卷
  - /opt/data:/var/lib/mysql
  // 以 Compose 配置文件为中心的相对路径作为数据卷挂载到容器。
  - ./cache:/tmp/cache
  // 使用用户的相对路径（~/ 表示的目录是 /home/<用户目录>/ 或者 /root/）。
  - ~/configs:/etc/configs/:ro
  // 已经存在的命名的数据卷。
  - datavolume:/var/lib/mysql
  ```

  如果不使用宿主机的路径，可以指定一个volume_driver。
  `volume_driver: mydriver`