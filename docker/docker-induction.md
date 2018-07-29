> 内容源自阅读 [`第一本 Docker 书`](https://item.jd.com/11909234.html)

### 确保 Docker 已经就绪

**查看 docker 程序是否正常工作**

```
docker info
Containers: 33
 Running: 1
 Paused: 0
 Stopped: 32
Images: 83
Server Version: 18.03.1-ce
...
```

### 运行第一个容器

docker run 命令提供了容器创建到启动的功能。

**运行第一个容器**

```
docker run -i -t ubuntu /bin/bash
```

我们告诉 Docker 执行 docker run 命令，并指定了 -i 和 -t 两个命令行参数。`-i` 标志保证容器中 STDIN 是开启的，`-t` 标志告诉 Docker 为创建的容器分配一个伪 tty 终端。

接下来，我们告诉 Docker 基于什么镜像来创建容器。Docker 首先会检查本地是否存在基于的镜像，如果没有的话，那么会连接官方的 `Docker Hub Registry`。查找并下载到本地宿主机中。

随后，Docker 在文件系统内用这个镜像创建了一个新容器。该容器拥有自己的网络、IP 地址，以及一个用来和宿主机进行通信的桥接网络接口。

最后，我们告诉 Docker 在容器中运行什么命令。

### 使用第一个容器

**检查容器的主机名**

```
# 容器的 Id 就是主机名
root@0fc7624f1b0e:/# hostname
0fc7624f1b0e
```

**检查 /etc/hosts 文件**

```
root@0fc7624f1b0e:/# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      0fc7624f1b0e
```

Docker 在 hosts 文件中为容器的 IP 地址添加了一条主机配置项。

**检查进程**

```
root@0fc7624f1b0e:/# ps -aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.1  18504  3400 pts/0    Ss   12:30   0:00 /bin/bash
root        214  0.0  0.1  34396  2904 pts/0    R+   12:37   0:00 ps -aux
```

**安装 vim**

```
apt-get update && apt-get install vim
```

在容器中可以做任何事情。输入 exit，就能退出返回到宿主机。一旦退出容器，/bin/bash 命令就结束； ，容器也随之停止了运行。

**列出容器列表**

```
docker ps -a
```

默认情况下，使用 docker ps 只能看到运行中的容器。如果指定了 `-a` 标志，那么会列出所有容器。

> 有 3 种方式可以唯一指代容器：短 UUID、长 UUID 或者名称。

### 容器命名

默认情况下 Docker 为创建的容器自动生成一个名称。如果想指定名称可以用 `--name` 标志实现。

一个合法的容器名称只能包含以下字符：大小写字母、数字、下划线、圆点、横线。（用正则表示就是 `[a-zA-z0-9_.-]`）

容器的名称必须是唯一的。如果创建两个相同名称的容器，命令将会失败。如果使用的容器名称已存在，可以使用命令 `docker rm ` 删除后，在创建新的容器。

### 重新启动停止的容器

```
docker start kind_khorana
docker start 0fc7624f1b0e
```
容器重新启动的生活，会沿用 docker run 命令时指定的参数运行。

### 附着到容器上

```
docker attach kind_khorana
docker attach 0fc7624f1b0e
```

### 创建守护式容器

除了交互式容器，也可以使用 `-d` 标志创建长期运行的容器。守护式容器没有交互回话，非常实用运行应用程序和服务。

```
docker run --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

### 查看容器日志

```
docker logs daemon_dave
# 跟踪日志输出 持续输出
docker -f logs daemon_dave
# 输入日志前记上时间戳
docker -t logs daemon_dave
# 查看最近 10 条日志
docker logs --tail 10 daemon_dave
```

### 查看容器内的进程

```
docker top daemon_dave
```

### 统计信息

```
docker stats daemon_dave
```

### 在容器内部运行进程

```
# 运行一个后台进程
docker exec -d daemon_dave touch /etc/new_config_file
# 打开交互式会话
docker exec -i -t daemon_dave /bin/bash
```

### 停止守护式容器

```
docker stop daemon_dave
```

### 自动启动容器

由于某些错误而导致容器停止运行，可以通过 `--restart` 标识，让 Docker 自动重新启动容器。 `--restart` 检测容器的退出代码，并据此决定是否重启容器。

```
docker run --restart=always --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello word; sleep 1; done"
```

`--restart` 标志被设置为 **always**。无论容器的退出代码是什么，Docer 都会自动重启该容器。除了 **always**，还可以设为 **on-on-failure**，这样只有当容器退出代码为非 0 值时，才会自动重启，另外还可以设置重启次数参数 `--restart=on-failure:5`

### 深入容器

docker inspect 命令会对容器进行详细的检查，然后返回配置信息，包括名称、命令、网络配置等数据。

```
# 获取所有信息
docker inspect daemon_dave
# 获取指定信息
docker inspect --format='{{.State.Running}}' daemon_dave
# 获取多个容器信息
docker inspect daemon_dave webserver
```

### 删除容器

```
# 删除指定容器
docker rm daemon_dave
# 删除所有容器
docker rm `docker pa -aq`
# 删除所有容器
docker container prune 
```
