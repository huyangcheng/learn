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
