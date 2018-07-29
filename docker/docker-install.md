> 内容源自阅读 [`第一本 Docker 书`](https://item.jd.com/11909234.html)

### 安装的先决条件

Docker 要求的条件具体如下：

+ 运行 64 为 CPU 架构的计算机。
+ 运行 Linux 3.8 及以上的内核。
+ 内核必须支持一种适合的存储驱动，例如：
    * Device Manager。
    * AUFS。
    * vfs。
    * btrfs。
    * ZFS。
+ 内核必须支持开启 cgroup 和 命名空间（namespace） 功能。

### 在 WIN 10 中安装 Docker for windows

Docker for windows 是在 Windows 10 Professional或Enterprise 64位系统下，使用Windows本机Hyper-V虚拟化和网络，是在Windows上开发Docker应用程序的最快，最可靠的方法。

对于以前的版本需要使用 Docker Toolbox

**安装**
下载 [Docker for Windows Installer](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe
)，双击Docker for Windows Installer以运行安装程序。

安装完成后，Docker会自动启动。通知区域中的鲸鱼表示Docker正在运行，并且可以从终端访问。



