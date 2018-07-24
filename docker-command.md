### 常用命令
+ docker --version ：查看版本信息
+ docker info ：查看 docker 信息
+ docker run IMAGE : 运行一个容器
    * -i ：保证容器中的 STDIN 是开启的
    * -t ：为创建的容器分配一个伪 tty 终端
    * --name ：为容器自定义名称
    * -d ：将容器放到后台运行
    * -p ：设置端口映射，如 **80:80**，**127.0.0.1:8080:80**
+ docker ps ：显示正在运行的容器
    * -a ：列出所有容器，包括运行和停止的
    * -l ：列出最后一个运行的容器
    * -n number：列出最后 number 个容器
    * -q ：只返回容器 Id

+ docker start [name|id] ：根据容器名或容器 Id 启动容器
+ docker stop [name|id] ：根据容器名或容器 Id 停止容器
+ docker restart [name|id] ：根据容器名或容器 Id 重启容器
+ docker rm [name|id] ：根据容器名删除容器
+ docker attach [name|id] ：容器重新启动后，会沿用 docker run 命令时的参数运行，使用此
命令可以重新附加到容器的会话上
+ docker log [name|id] ：获取容器日志
    * -f ：持续监听容器日志
    * --tail number ：获取日志的最后 number 行
    * -t ：给每一行日志加上时间戳
+ docker top [name|id] ：查看容器内的进程
+ docker top [name|id]... ：查看所有容器或指定容器统计信息
+ docker exec [name|id] command ：对正在运行中的容器执行命令
    * -d ：以后台进程方式执行命令
    * -i ：保证容器中的 STDIN 是开启的
    * -t ：为创建的容器分配一个伪 tty 终端
+ docker inspect [name|id]... ：获取容器信息
    * -f，-format ：输出指定模板，如：`{{.State.Running}}`
+ docker images [name]：列出所有或指定名称的镜像
+ docker pull name[:tag] ：从镜像仓库中拉取镜像
+ docker search name ：从镜像仓库中查看镜像
+ docker commit [name|id] mirror_name[:tag] ：将指定容器创建为一个新的镜像，还可以设置镜像标签
    * -m ：设置提交信息
    * -a ：设置作者
+ docker build [OPTIONS] PATH | URL | - ：从指定路径或者地址上根据 `Dockerfile` 构建一个新的镜像
    * -t ：设置名称或者标签
    * --no-cache ：不使用缓存进行构建
+ docker history IMAGE ：显示镜像的构建历史
+ docker port IMAGE port ：查看镜像端口影射情况


### 查看 Docker 端口影射情况

```
docker port 8cb23f33fa30 80
```