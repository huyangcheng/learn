> 内容源自阅读 [`第一本 Docker 书`](https://item.jd.com/11909234.html)

Docker 提供三种方式让容器间进行连接

+ Docker 的内部网络。
+ Docker Networking，1.9 及之后的版本开始。
+ Docker 链接。一个可以将具体容器链接到一起来进行通信的抽象层。

> 推荐使用 Docker Networking 的方式

### 内部网络

> 直接使用容器的 IP 进行连接，但是重启后容器的 IP 会变化。

在安装 Docker 时，会创建一个新的网络接口，名字是 docker0。每个容器都会在这个接口上分配一个IP地址。

Docker 默认使用 172.17.x.x 作为子网地址，如果这个子网被占用了，会在 172.16 ~ 172.30 这个范围内尝试创建子网。

Docker 每创建一个容器就会创建一组互联的网络接口。这组接口其中一端作为容器里的 `eth0` 接口，另一端统一命名为 `vethec6a` 这种名字作为宿主机的端口。

可以把 veth 接口认为是虚拟网线的一端。这个虚拟网线一端插在 docker0 网桥上，另一端插在容器里。通过每个 veth* 接口绑定到 docker0 网桥，Docker 创建了一个虚拟子网，这个子网由宿主机和所有的 Docker 容器共享。

### Docker Networking

容器之间的连接用网络创建，这被称为 `Docker Networking`，也是 Docker 1.9 发布的新特性。

Docker Networking 允许用户创建自己的网络，容器可以通过这个网上互相通信。实质上，Docker Networking 以新的用户管理的网络补充现有的 `docker0`。更重要的是，现在容器可以跨越不同的宿主机来通信，并且网络配置可以更灵活地定制。

一个容器可以`隶属于多个` Docker 网络，所以可以创建非常复杂的网络模型。

**创建 Docker 网络**

```
# 创建一个名为 app 的桥接网络
docker network app
```

**查看 app 网络详情**
```
# 查看名称 app 的网络详情
docker network inspect app
```

**指定容器的运行网络**

```
# 指定运行网络为 app
docker run -d -p 6379:6379 --net=app --name redis xhz/redis
```

运行在 Docker Networking 的容器间，使用容器名称就能连接，也可使用容器名称.app。如：`ping redis` 或者 `ping redis.app`

**将已有容器连接到 Docker Networking**

```
# 将正在运行的 ubuntu 容器连接至 app 网络上
docker network connect app ubuntu
```

**从网络中断开一个容器**

```
# 将 ubuntu 容器从 app 网络中断开
docker network disconnect app ubuntu
```

### Docker 链接

Docker 链接是 1.9 之前的首选容器连接方式。让一个容器链接到另一个容器是一个简单的过程，这个过程要引用容器的名字。

**启动 Redis 容器**
```
docker run -d --name redis xhz/redis
```

**连接 Redis 容器**

```
 docker run -d -p 80:80 --name nginx --link redis:db xhz/nginx nginx
```

启动时使用 `--link` 标志创建两个容器间的客户-服务链接。这个标志需要两个参数：一个是被链接的容器名称，另一个是链接的别名。

在这个例子中，nginx 容器是客户，redis 容器是服务，并且服务别名为db。

在启动 Redis 容器时，并没有公开端口。因为通过把容器链接在一起，可以让客户容器直接访问任何服务容器的公开端口。并且只有使用 `--link` 标志链接到 Redis 容器的容器才能连接到这个端口。

> 在启动 Docker 守护进程时加上 `--icc=false` 标志，可以强制 Docker 只允许有链接的容器之间互相通信。

Docker 在客户容器的以下两个地方写入了链接信息。

+ /etc/hosts 文件中。
+ 包含链接信息的环境变量中

**查看 /etc/hosts 文件**
```
root@9bfd15b2f83d:/# cat /etc/hosts
...
172.17.0.2      db 633d45c03983 redis
172.17.0.3      9bfd15b2f83d
```

**查看用于连接的环境变量**

```
root@9bfd15b2f83d:/# env
...
DB_NAME=/nginx/db
DB_PORT_6379_TCP_PORT=6379
DB_PORT=tcp://172.17.0.2:6379
DB_PORT_6379_TCP=tcp://172.17.0.2:6379
DB_ENV_REFRESHED_AT=2018-07-28
DB_PORT_6379_TCP_ADDR=172.17.0.2
DB_PORT_6379_TCP_PROTO=tcp
```

Docker 在客户容器中自动创建了以链接的别名 `DB` 开头的环境变量。


使用容器连接的通信方式：

+ 使用环境变量的连接信息
+ 使用 DNS 和 /etc/hosts 信息

**连接 redis 容器**
```
# 使用环境变量
redis-cli -h $DB_PORT_6379_TCP_ADDR
# 使用本地DNS
redis-cli -h db
```

### 其他

**xhz/ubuntu ubuntu**
```
FROM ubuntu:14.04
MAINTAINER xhz "xhz@xx.com"
ENV REFRESHED_AT 2018-07-28
RUN sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/mirrors\.163\.com\/ubuntu\//g' /etc/apt/sources.list
RUN apt-get -yyq update
```
**xhz/nginx Dockerfile**
```
FROM xhz/ubuntu
MAINTAINER hxz "xhz@xx.com"
ENV REFRESHED_AT 2018-07-26
RUN apt-get -yqq install nginx
RUN mkdir -p /var/www/html/website
ADD nginx/global.conf /etc/nginx/conf.d
ADD nginx/nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
```

**xhz/redis Dockerfile**

```
FROM xhz/ubuntu
RUN apt-get -yqq install redis-server redis-tools
EXPOSE 6379
ENTRYPOINT ["/usr/bin/redis-server"]
CMD []
```
