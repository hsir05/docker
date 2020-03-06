# docker-comose

**常用命令**

1. 显示正在运行的进程

    `docker-compse top`
    -----

2. docker-compose up

    `docker-compose up`
    ---

    ```zsh
    -d 在后台运行服务容器
    –no-color 不使用颜色来区分不同的服务的控制输出
    –no-deps 不启动服务所链接的容器
    –force-recreate 强制重新创建容器，不能与–no-recreate同时使用
    –no-recreate 如果容器已经存在，则不重新创建，不能与–force-recreate同时使用
    –no-build 不自动构建缺失的服务镜像
    –build 在启动容器前构建服务镜像
    –abort-on-container-exit 停止所有容器，如果任何一个容器被停止，不能与-d同时使用
    -t, –timeout TIMEOUT 停止容器时候的超时（默认为10秒）
    –remove-orphans 删除服务中没有在compose文件中定义的容器
    –scale SERVICE=NUM 设置服务运行容器的个数，将覆盖在compose中通过scale指定的参数
    ```

3. 启动所有服务

    `docker-compose up`
    ---

4. 在后台所有启动服务

    `docker-compose up -d`
    ---

5. -f 指定使用的Compose模板文件，默认为docker-compose.yml，可以多次指定

    `docker-compose -f docker-compose.yml up -d`
    ---

6. 列出项目中目前的所有容器

    `docker-compose ps`
    ---

7. 停止正在运行的容器，可以通过docker-compose start 再次启动

    `docker-compose stop`
    ---

8. 停止和删除容器、网络、卷、镜像

    `docker-compose down [options]`
    ---

    ```zsh
    –rmi type，删除镜像，类型必须是：all，删除compose文件中定义的所有镜像；local，删除镜像名为空的镜像
    -v, –volumes，删除已经在compose文件中定义的和匿名的附在容器上的数据卷
    –remove-orphans，删除服务中没有在compose中定义的容器
    ```

    停用移除所有容器以及网络相关
    `docker-compose down`

9. 查看服务容器的输出
    
    查看服务容器的输出。默认情况下，docker-compose将对不同的服务输出使用不同的颜色来区分。可以通过–no-color来关闭颜色

    `docker-compose log`
    ---
10. 构建（重新构建）项目中的服务容器

    `docker-compose build`
    ---
    ```zsh
    –compress 通过gzip压缩构建上下环境
    –force-rm 删除构建过程中的临时容器
    –no-cache 构建镜像过程中不使用缓存
    –pull 始终尝试通过拉取操作来获取更新版本的镜像
    -m, –memory MEM为构建的容器设置内存大小
    –build-arg key=val为服务设置build-time变量
    ```
11. 拉取服务依赖的镜像

    `docker-compose pull`
    ---
    docker-compose pull [options] [SERVICE...]
    
    ```zsh
        –ignore-pull-failures，忽略拉取镜像过程中的错误
    –parallel，多个镜像同时拉取
    –quiet，拉取镜像过程中不打印进度信息
    docker-compose pull
    ```
12. 重启项目中的服务

    `-t, –timeout TIMEOUT，指定重启前停止容器的超时（默认为10秒`
    `docker-compose start [SERVICE...]`
    `docker-compose start`
    ---

13. 设置指定服务运行的容器个数。通过service=num的参数来设置数量

    `docker-compose scale web=3 db=2`
    ---
14. 通过发送SIGKILL信号来强制停止服务容器

    `docker-compose kill -s SIGINT`
    --
15. 验证并查看compose文件配置

    `docker-compose config [options]`

    ```zsh
    –resolve-image-digests 将镜像标签标记为摘要
    -q, –quiet 只验证配置，不输出。 当配置正确时，不输出任何内容，当文件配置错误，输出错误信息
    –services 打印服务名，一行一个
    –volumes 打印数据卷名，一行一个
    ```
16. 显示某个容器端口所映射的公共端口

    `docker-compose port`

    ```zsh
     –protocol=proto，指定端口协议，TCP（默认值）或者UDP
     –index=index，如果同意服务存在多个容器，指定命令对象容器的序号（默认为1)
    ```