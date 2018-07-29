> 内容源自阅读 [`第一本 Docker 书`](https://item.jd.com/11909234.html)

编配是一个没有严格定义的概念。这个概念大概描述了自动配置、协作和管理服务的过程。

编配用来描述一组实践过程，这个过程会管理运行在多个 Docker 容器里的应用，而这些容器可能运行在多个宿主机上。

Docker compose 是官方提供的简单容器编配工具。

### docker-compose.yml 文件

在 Compose 中，我们定义了一组要启动的服务，还定义服务启动运行时的属性，这些属性和 docker run 命令需要的参数类似。

将所有与服务有关的属性都定义在一个 YAML 文件里，之后执行 `docker-compose up` 命令，Compose 会使用指定的参数启动这些容器，并将所有日志输出合并到一起。

**docker-compose.yml**

```
# 服务名称
web:
  # 要使用的镜像    
  image: xhz/nginx
  # 执行的命令
  command: nginx
  # 端口的映射
  ports:
    - "80:80"
  # 卷的挂载
  volumes:
    - f:/learn/docker/sample/website:/var/www/html/website
  # 需要链接的容器
  links:
    - redis
redis:
  image: xhz/redis
```

**运行 Compose**
```
# 方式1 
docker-compose up
# 方式2 以守护进程的模式运行
docker-compose up -d

Creating compose_redis_1 ... done
Creating compose_web_1   ... done
Attaching to compose_redis_1, compose_web_1
....
```

为了保证服务的唯一，Compose 将 docker-compose.yml 中的服务名称加上目录名作为前缀，并分别用数字作为后缀。

**常用命令**

```
# 列出所有服务
docker-compose ps
# 显示所有日志
docker-compose logs
# 停止运行的服务
docker-compose stop
# 杀死运行的服务
docker-compose kill
# 重启启动停止的服务
docker-compose start
# 删除服务
docker-compose rm
```