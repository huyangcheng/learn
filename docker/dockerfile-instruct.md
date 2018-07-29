> 内容源自阅读 [`第一本 Docker 书`](https://item.jd.com/11909234.html)

### CMD

CMD 指令用于容器启动时要运行的命令，它和 **RUN** 指令类似，只是 **RUN** 指令是指定镜像被构建时运行的命令，但是和使用 docker run 命令启动容器时指定运行的命令类似。
如果启动容器时使用了相同的命令，启动容器时的命令会覆盖 CMD 中的命令。
CMD 指令只能指定一条，如果指定了多条也只有最后一条被使用

**启动容器时运行命令**

`docker run -i -t nginx /bin/true`

**使用 CMD 指令**

`CMD ["/bin/true"]`

上面这两个命令是等效的

**使用 CMD 指令传递参数**

`CMD ["/bin/bash", "-l"]`

将 -l 标志传递给 /bin/bash 命令

> 将运行的命令放在数组中，Docker 将按原样执行命令。如果直接将命令和参数写在一起，Docker 会在命令前加上 **/bin/sh -c**。这样执行命令时可能会导致意外的行为，所以推荐使用数组的语法来设置执行的命令。

### ENTRYPOINT

使用 ENTRYPOINT 指令后，在 docker run 命令后的参数都将被 ENTRYPOIN 指令接收

**ENTRYPOIN 指令**

`ENTRYPOINT ["/usr/sbin/nginx"]`

**启动命令**

`docker run -t -i xhz/static_web -g "daemon off;"`

上面的示例中，启动命令后的参数将被传递至 ENTRYPOINT 中，启动后执行 **/usr/sbin/nginx -g -g "daemon off;"** 命令。

**同时使用 ENTRYPOIN 和 CMD 指令**

```
ENTRYPOINT ["/usr/sbin/nginx"]
CMD ["-h"]
```

如果在启动容器时指定了参数，那么参数将传递至 ENTRYPOINT 中。如果没有指定参数，那么使用 CMD 中的命令传递至 ENTRYPOINT 中。这样使我们构建的镜像即可运行默认的命令，同时也指出通过 docker run 命令指定可覆盖的选项或标志。

通过 docker run 运行时 使用 `--entrypoint` 可以覆盖ENTRYPOINT 指令。

### WORKDIR

WORKDIR 指令用于在容器内部设置一个工作目录，ENTRYPOINT / 或 CMD 指定的程序会在这个目录下执行。

运行容器时使用 `-w` 覆盖工作目录

**使用示例**
```
WORKDIR /opt/webapp/db
RUN bundle install
WORKDIR /opt/webapp
ENTRYPOINT ["rackup"]
```

### ENV

ENV 指令用于在构建过程中设置环境变量，这个环境变量可以在后续的指令中使用，并且这些环境变量被持久化保存到从镜像创建的容器中。

运行容器时使用 `-e` 覆盖同名环境变量

**设置多个环境变量**

```
# 设置 RVM_PATH，RVM_ARCHFLAGS 环境变量
ENV RVM_PATH=/home/rvm RVM_ARCHFLAGS="-arch i386"
```

**Dockerfile 中使用环境变量**

```
ENV TARGET_DIR /opt/app
WORKDIR $TARGET_DIR
```

**容器中获取 evn 环境变量**

```
docker run -i -t xhz/test:v1
root@5a30121d723f:/# env
RVM_ARCHFLAGS=-arch i386
RVM_PATH=/home/rvm

```

**运行时覆盖 Dockerfile 中的环境变量**

```
docker run -i -t -e "RVM_PATH=123" xhz/test:v1
root@5a30121d723f:/# env
RVM_ARCHFLAGS=-arch i386
RVM_PATH=123
```

### USER

USER 指令用于指定镜像运行的用户，我们可以指定用户名，UID，组，GID， 或者两者的组合。

设置 USER 指令后的指令运行用户都使用设置的用户，指定用户时必须先创建用户，如果不指定默认用户为 `root`。

**示例**
```
USER user
USER user:group
USER uid
USER uid:gid
USER user:gid
USER uid:group
```

**指定用户 Dockerfile**

```
# Version 0.0.1
FROM ubuntu:latest
MAINTAINER xhz "xhz@qq.com"
# 先创建用户
RUN groupadd -r xhz && useradd -r -g xhz xhz
# 指定用户
USER xhz
CMD ["/bin/bash"]
```

### VOLUME

VOLUME 用于基于镜像创建的容器添加卷，一个卷是可以存在于一个或多个容器内特定的目录，这个目录可以绕过联合文件系统，并提供如下共享数据或对数据进行持久化的功能。

+ 卷可以在容器间共享和重用。
+ 一个容器可以不是必须和其他容器共享卷。
+ 对卷的修改是立即生效的。
+ 对卷的修改不会对更新镜像产生影响。
+ 卷会一直存在直到没有任何容器使用它。

卷功能可以让我们将数据、数据库或者其他内容添加到镜像中而不是将这些内容提交到镜像中，并且允许我们在多个容器间共享这些内容。

我们可以利用此功能来测试容器和内部的应用程序代码，管理日志，或者处理容器内部的数据库。

**示例**
```
VOLUME ["/opt/project", "/data"]
```

### ADD
> ADD 指令会使`构建缓存无效`。

ADD 指令用于将构建环境下的文件和目录复制到镜像中。

Docker 通过地址参数末尾的字符来判断文件源是目录还是文件。以 / 结尾判定为目录，否则判定为文件。

如果目的位置不能存在，Docker 会创建这个全路径。新创建的文件和目录模式为 `0755`，并且 UID 和 GID 都是 0。



```
# 复制构建目录下的文件
ADD docker.md /data/docker.md
# 复制网络资源的文件
ADD https://www.cnblogs.com//images/logo_small.gif /data/test.gif
# 复制网络资源的文件 不设置文件名
ADD https://www.cnblogs.com//images/logo_small.gif /data/test/
# 构建目录下的复制归档文件 归档文件自动进行解压
ADD log.tar.gz /data/log
# 使用相同的路径，后面的会覆盖前面的
ADD 1.txt /data/1.txt
ADD 2.txt /data/1.txt
```

### COPY

> COPY 是 Docker 1.0 发布时新增的指令，尽量使用 COPY `代替` ADD。

COPY 指令类似于 ADD，它们根本的不同是 COPY 只关心在构建上下文中复制本地文件，而不去做文件提取和解压。

### LABEL

LABEL 指令用于为 Docker 镜像添加元数据，元数据以键值对的形式展现。

多个元数据的情况下，推荐使用一个指令进行完成，以防止不同的元数据指令创建过多的镜像层。

```
# 指定一个元数据
LABEL version="1.0"
# 指定多个元数据 使用空格分割
LABEL name="xhz" tag="v1"
```

### STOPSIGNAL

STOPSIGNAL 指令用于设置停止容器时发送什么系统调用信号给容器。这个信号必须是内核系统调用表中的合法的数。

### ARG

ARG 指令用于定义 docker build 命令运行时传递给构建运行时的变，只需要使用 `--build-arg` 标志即可。用户只能在构建时指定在 Dockerfile 文件中定义过的参数。

**添加 ARG 指令**
```
ARG build
ARG webapp_user=user
```

**使用 ARG 指令**
```
docker build --build-arg build=1234 -t "xhz/test" .
```

**预定义 ARG 变量**

+ HTTP_PROXY
+ http_proxy
+ HTTPS_PROXY
+ https_proxy
+ FTP_PROXY
+ ftp_proxy
+ NO_PROXY
+ no_proxy

### ONBUILD

ONBUILD 指令用于为镜像添加触发器，当一个镜像被用作其他镜像的基础镜像时，镜像中的触发器将会被执行。

触发器会在构建过程中插入新指令，这些指令是经跟在 `FROM` 之后，触发器可以是除了 `FROM`、`MAINTAINER` 和 `ONBUILD` 之外的任何构建指令。

触发器只能被继承一次，也就是只能在子镜像中执行，而不会在孙子镜像中执行。

**示例**

```
ONBUILD ADD . /app/src
ONBUILD RUN cd /app/src && make
```
