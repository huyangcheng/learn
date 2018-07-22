### 运行第一个 `docker` 容器

-i 保证容器中 STDIN 是开启的，-t 是告诉 docker 为创建的容器分配一个伪 tty 终端
```
docker run -i -t ubuntu bash
```
### 自动启动容器

由于某些错误而导致容器停止运行，可以通过 `--restart` 标识，让 Docker 自动重新启动容器。 `--restart` 检测容器的退出代码，并据此决定是否重启容器。

```
docker run --restart=always --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello word; sleep 1; done"
```

`--restart` 标志被设置为 **always**。无论容器的退出代码是什么，Docer 都会自动重启该容器。除了 **always**，还可以设为 **on-on-failure**，这样只有当容器退出代码为非 0 值时，才会自动重启，另外还可以设置重启次数参数 `--restart=on-failure:5`

### 获取容器信息

```
# 获取所有信息
docker inspect daemon_dave
# 获取指定信息
docker inspect --format='{{.State.Running}}' daemon_dave
# 获取多个容器信息
docker inspect daemon_dave webserver
```

### 常用命令
+ docker --version ：查看版本信息
+ docker info ：查看 docker 信息
+ docker run IMAGE : 运行一个容器
    * -i ：保证容器中的 STDIN 是开启的
    * -t ：为创建的容器分配一个伪 tty 终端
    * --name ：为容器自定义名称
    * -d ：将容器放到后台运行
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
