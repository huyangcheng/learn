### build apt-get 慢

将 apt-get 镜像源修改为国内

```
RUN sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/mirrors\.163\.com\/ubuntu\//g' /etc/apt/sources.list
RUN apt-get -yqq update && apt-get -yqq install nginx
```

### exec echo 无法操作文件

错误示例
```
# 系统找不到指定的路径
docker exec 67bab16ccd59 echo 123 >  /var/www/html/website/index.html
# no such file or directory
docker exec 67bab16ccd59 "echo 123 >  /var/www/html/website/index.html"

```

正确示例
```
docker exec 67bab16ccd59 bash -c "echo 123 >  /var/www/html/website/index.html"
```

### 设置端口启动容器失败提示：`Error starting userland proxy: mkdir xxxxxx input/output error.`

未找到正确原因，根据 [GitHub Issues](https://github.com/docker/for-win/issues/573) 提示重启 Docker 即可恢复正常。

### 无法安装JDK8，提示 `Unable to locate package openjdk-8-jdk`

```
# 无法使用 add-apt-repository 时执行此命令
RUN apt-get install -yqq software-properties-common
# 添加 JDK 8 的仓库
RUN add-apt-repository ppa:webupd8team/java
# 更新软件源
RUN apt-get update
```

