> 内容源自阅读 [`第一本 Docker 书`](https://item.jd.com/11909234.html)

### 什么是 Docker 镜像

Docker 镜像是`由文件系统叠加而成`。最底端是一个引导文件系统，即 bootfs。

Docker 用户几乎永远不会和引导文件系统有什么交互，当一个容器启动后，它会被移到内存中，而引导文件系统则被卸载，以留出更多的内存供 initrd 磁盘使用。

Docker 镜像的第二层是` root 文件系统 rootfs`，它位于引导文件系统之上。rootfs 可以是一种或多种操作系统。

Docker 中的 root 文件系统`永远都是只读状态`，并且 Docker 利用**联合加载技术**在 root 文件系统层上加载更多的只读文件系统。联合加载指的是一次同事加载多个文件，但外面看起来只能看到一个文件系统。联合加载会将各层文件叠加到一起，这样最终的文件系统会包含所有底层的文件和目录。

一个镜像可以放在另一个镜像的顶部，位于下面的镜像称为父镜像，直到镜像栈的最底部，最底部的镜像被称为基础镜像。当一个镜像启动容器时， Docker 会在该镜像的最顶层加载一个读写文件系统，我们运行的程序就是在这个读写层中执行。

![`Docker 文件系统层`](http://huyangcheng.com/img/docker/docker-fs.jpg)

当第一次启动容器时，初始的读写层是空的。当文件系统发生变化时，这些变化都会应用到这一层上。

比如，如果想修改一个文件，这个文件首先`从读写层下的只读层复制到读写层`。该文件的只读版本依然存在，但是已经被读写层中的文件副本所隐藏。

通常这种机制被称为`写时复制`，这也是 Docker 强大的技术之一。每个只读镜像层都是只读的，并且以后永远都不会变化。

当创建新容器时，Docker 会构建出一个镜像栈，并在最顶端添加一个读写层。这个读写层在加上其下面的镜像层以及一些配置数据，就构成了一个容器。

### 列出镜像

```
docker images
```

本地镜像都保存在 Docker 宿主机的 /var/lib/docker 目录下。每个镜像都保存在 Docker 所采用的存储驱动目录下面，可以在 /var/lib/docker/containers 目录下看到所有容器。

### 拉取镜像

```
# 默认拉取 latest 标签的镜像
docker pull ubuntu
# 拉取指定标签的镜像
docker pull ubuntu:14.04
```

为了区分同一个仓库中的不同镜像， Docker 提供了一种标签的功能。每个镜像在列出来时都带有一个标签。每个标签对组成特定镜像的一些镜像层进行标记，这种机制使得同一个仓库中可以存储多个镜像。

### 查找镜像

```
docker search nginx
```

### 构建镜像

构建镜像有以下两种方法。

+ docker commit 命令
+ docker build 命令和 Dockerfile 文件

推荐使用更灵活、更强大的 Dockerfile 来构建镜像。

#### docker commit 构建镜像

docker commit 就是我们先创建一个容器，并在容器中做出修改，最后将修改提交为一个新镜像。

**创建容器并做出修改**

```
# 创建容器
docker run -i -t ubuntu /bin/bash
# 做出修改（输入内容到指定文件中）
root@1b1c06405aad:/# echo docker commit create mirror > ~/build.log
# 退出容器
exit
```

**提交定制容器**

使用 docker commit 命令指定要提交的修改过的容器 ID，以及一个目标镜像仓库和镜像名。

```
docker commit 1b1c06405aad xhz/ubuntu_commit
```

**指定更多提交信息**

```
docker commit -m "commit message" -a "xhz" 1b1c06405aad xhz/ubuntu_commit:v1
```

这里使用 `-m` 标志描述提交信息，使用 `-a` 标志列出镜像的作者信息，并为镜像增加了一个 `v1` 标签。

**从提交的镜像运行一个容器**
```
docker run -i -t xhz/ubuntu_commit:v1 /bin/bas
root@53e07e69553d:/# cat ~/build.log
docker commit create mirror
```

#### Dockerfile 构建镜像

推荐使用被称为 `Dockerfile` 的定义文件和 `docker build` 命令来构建镜像。Dockerfile 使用基本的基于 DSL 语法的指令来构建一个镜像，因为这种构建方式更具备**可重复性**、**透明性**及**幂等性**。

**创建一个仓库**

```
mkdir statis_web
cd static_web
touch Dockerfile
```

statis_web 目录来用保存 Dockerfile，这个目录也是构建环境，Docker 称此为`环境上下文`或者`构建上下文`。

Docker 会在构建镜像时将构建上下文和该上下文中的文件和目录上传到守护进程。这样守护进程就能直接访问用户想在镜像中存储的任何代码、文件或者其他数据。

**Dockerfile**

```
FROM ubuntu:14.04
MAINTAINER xhz "xhz@xx.com"
# 替换软件源
RUN sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/mirrors\.163\.com\/ubuntu\//g' /etc/apt/sources.list
RUN apt-get update && apt-get install -y nginx
RUN echo 'Hi, I am in your container' > /usr/share/nginx/html/index.html
EXPOSE 80
```

Dockerfile 由一系列指令和参数组成。每一条指定都必须为大写字母，且后面跟随一个参数。指令会按照顺序从上到下执行，所以应该根据需求合理安排指令的顺序。

每条指令都会创建一个新的镜像层并对镜像进行提交。大体上按照如下流程执行指令：

+ Docker 从基础镜像运行一个容器。
+ 指定一条指令，对容器进行修改。
+ 执行类似 docker commit 的操作，提交一个新的镜像层。
+ Docker 在基于刚提交的镜像运行一个新容器。
+ 执行下一条指令，直到所有指令都执行完毕。

**基于 Dockerfile 构建镜像**

如果没有指定任何标签，Docker 将会自动为镜像设置一个 `latest`标签。

```
docker build -t xhz/static_web .
...
Successfully built 4fa448b5b5d6
Successfully tagged xhz/static_web:latest
...
```

**从 Git 仓库中构建镜像**

如果 Git 仓库的根目录存在 Dockerfile 也会执行构建

```
docker build -t xhz/static_web:v1 git@github.com:jamtur01/docker-static_web
```

**不使用 Dockerfile 名称的文件作为构建文件**

通过 `-f` 标志指定一个区别于标准 Docker 构建源的位置，这个文件不必命名为 Dockerfile，但是必须唯一构建上下文之中。

```
cp Dockerfile new-Dockerfile
docker build -t xhz/static_web:v2 -f new-Dockerfile .
# 将构建上下文上传到守护进程
Sending build context to Docker daemon  3.072kB
```

如果构建上下文目录存在以 `.dockerignore` 命名的文件，那么该文件内容会被按行进行分割，每一行都是一条文件过滤匹配模式。这很像 .gitignore 文件，该文件用来设置那些文件不会被当作构建上下文的一部分，因此可以防止它们被上传到守护进程中。











