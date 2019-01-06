# docker_practice
> 近几年docker越来越火，我们公司也在项目中使用了微服务+docker的组合，最近尝试着搭建了一套docker的环境，下面是环境的搭建以及简单地使用

###  什么是docker

直接引用百度百科的介绍：

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

一个完整的Docker有以下几个部分组成：
1. dockerClient客户端
2. Docker Daemon守护进程
3. Docker Image镜像
4. DockerContainer容器

Docker 架构

Docker采用 C/S架构 Docker daemon 作为服务端接受来自客户的请求，并处理这些请求（创建、运行、分发容器）。 客户端和服务端既可以运行在一个机器上，也可通过 socket 或者RESTful API 来进行通信。

Docker daemon 一般在宿主主机后台运行，等待接收来自客户端的消息。 Docker 客户端则为用户提供一系列可执行命令，用户用这些命令实现跟 Docker daemon 交互。

###  搭建docker环境

之前一直都想在自己的win7 32位系统上搭建一个docker的环境，后来发现，docker不支持32位的Linux或Windows系统，于是就弄了个天翼的云主机，在上面装了Ubuntu Linux系统以及docker（现在很多互联网大公司都推出来云主机产品，像阿里云，腾讯云，百度云，京东云，天翼云等等，我自己也购买了三年的腾讯云主机，感觉还行，云主机支持创建镜像以及回滚，还有各种Linux、Windows的系统镜像可供选择安装，安装的速度也非常快，很多云主机厂商都推出了学生版，说是要验证学生身份，其实也没什么验证，非学生也能注册成功，相同的配置，学生版相对来说便宜很多）。

####  安装Linux桌面版

一般的Linux云主机都是非桌面版的，只能通过远程连接工具，比如secureCRT、putty SSL上去，感觉用起来很不方便，如果部署了一个web应用，就不能通过浏览器本地访问了。

为此，我在网上查找了很多安装Linux桌面版的方法，都没有安装成功，差一点放弃了，不过最后还是安装成功了。我是参考这篇文章的：http://blog.51cto.com/12308777/2070963
我是使用的系统是天翼云的ubuntu 16.04，按照上面链接的文章进行操作之后，还需要执行如下Linux目录，开启1端口。
```
vncserver :1 
```
然后打开安装在Windows端的 vnc viewer并进行连接，连接成功后，就可以以图形界面的方式访问Linux系统了，如下图所示：

![VNC.jpg](https://upload-images.jianshu.io/upload_images/12273007-58b9e7bf1a4fd8ad.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

连接成功之后，可以看到Linux 系统图形界面如下：

![desktop.jpg](https://upload-images.jianshu.io/upload_images/12273007-969cf84b8a0c7c72.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 安装docker

我是用的系统是ubuntu 16.04，Docker 要求 Ubuntu 系统的内核版本高于 3.10 否则不支持 Docker。
通过 uname -r 命令可以查看内核版本：
```
wysh@wysh.site:~# uname -r
4.4.0-75-generic
```
可以使用如下脚本安装 Docker：
```
wysh@wysh.site:~# wget -qO- https://get.docker.com/ | sh
```
脚本执行完成之后，docker便安装成功了，是用如下命令可以启动docker 后台服务：
```
wysh@wysh.site:~# sudo service docker start
```
接着，我们可以测试运行hello-world，是用如下命令：

```
wysh@wysh.site:~# docker run hello-world
```
出现如下提示信息，说明docker已经安装成功了：

![hello.jpg](https://upload-images.jianshu.io/upload_images/12273007-d846271f6641cdf9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###  Docker Hello World

Docker 允许你在容器内运行应用程序， 使用 docker run 命令来在容器内运行一个应用程序。

输出Hello world

```
wysh@wysh.site:~# docker run ubuntu:15.10 /bin/echo "Hello world"
Hello world
```
各个参数解析：

-  docker: Docker 的二进制执行文件。

-  run:与前面的 docker 组合来运行一个容器。

-  ubuntu:15.10指定要运行的镜像，Docker首先从本地主机上查找镜像是否存在，如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像。

-  /bin/echo "Hello world": 在启动的容器里执行的命令

-  以上命令完整的意思可以解释为：Docker 以 ubuntu15.10 镜像创建一个新容器，然后在容器里执行 bin/echo "Hello world"，然后输出结果。

docker支持以交互的方式以及后台的方式运行

####  运行交互式的容器
我们通过docker的两个参数 -i -t，让docker运行的容器实现"对话"的能力
```
wysh@wysh.site:~# docker run -i -t ubuntu:15.10 /bin/bash
root@d434270470d6:/# 
```
各个参数解析：

-  -t:在新容器内指定一个伪终端或终端。

-  -i:允许你对容器内的标准输入 (STDIN) 进行交互。

此时我们已进入一个 ubuntu15.10系统的容器

我们尝试在容器中运行命令 cat /proc/version和ls分别查看当前系统的版本信息和当前目录下的文件列表

![run_docker.jpg](https://upload-images.jianshu.io/upload_images/12273007-126bdf2349b90b6c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以通过运行exit命令或者使用CTRL+D来退出容器。

####  启动容器（后台模式）

使用以下命令创建一个以进程方式运行的容器

```
wysh@wysh.site:~# docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
63f48e0a0d7f17009693e6f426b32d109fab683538d1a6d1c246427e83b4cf3a
wysh@wysh.site:~# 
```
在输出中，我们没有看到期望的"hello world"，而是一串长字符

63f48e0a0d7f17009693e6f426b32d109fab683538d1a6d1c246427e83b4cf3a

这个长字符串叫做容器ID，对每个容器来说都是唯一的，我们可以通过容器ID来查看对应的容器发生了什么。

首先，我们需要确认容器有在运行，可以通过 docker ps 来查看
```
wysh@wysh.site:~# docker ps
```

![ps.jpg](https://upload-images.jianshu.io/upload_images/12273007-b47fdab7bb6fc53c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

-  CONTAINER ID:容器ID

-  NAMES:自动分配的容器名称

-  在容器内使用docker logs命令，查看容器内的标准输出

####  停止容器
我们使用 docker stop 命令来停止容器（stop后面可以跟容器 id 或 name）:
```
wysh@wysh.site:~# docker stop 63f48e0a0d7f
```

###   Docker 实例
#### Docker 安装 MySQL
查找Docker Hub上的mysql镜像
```
wysh@wysh.site:~#  docker search mysql
```

![mysql.jpg](https://upload-images.jianshu.io/upload_images/12273007-8d96c8c1ae5ca635.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里我们拉取官方的镜像,标签为5.6

```
wysh@wysh.site:~# docker pull mysql:5.6
```
等待下载完成后，我们就可以在本地镜像列表里查到REPOSITORY为mysql,标签为5.6的镜像。

```
wysh@wysh.site:~# docker images |grep mysql
mysql               5.6                 27e29668a08a        8 days ago          256MB
wysh@wysh.site:~# 
```
####  使用mysql镜像
运行容器
```
wysh@wysh.site:~# docker run -p 3306:3306 --name mymysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
```
命令说明：

-  -p 3306:3306：将容器的 3306 端口映射到主机的 3306 端口。

-  -v -v $PWD/conf:/etc/mysql/conf.d：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf。

-  -v $PWD/logs:/logs：将主机当前目录下的 logs 目录挂载到容器的 /logs。

-  -v $PWD/data:/var/lib/mysql ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 。

-  -e MYSQL_ROOT_PASSWORD=123456：初始化 root 用户的密码。

查看容器启动情况
```
wysh@wysh.site:~# docker ps
```

![status.jpg](https://upload-images.jianshu.io/upload_images/12273007-76515302fb0f693d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####  运行容器内的mysql

-  进入容器
```
wysh@wysh.site:~# docker exec -it mymysql bash
```
-  登录mysql
```
root@66b2bb47e8ac:/# mysql -u root -p
```

-  查看 mysql 的数据库
```
mysql> show databases;
```
以上命令的执行结果如下图：

![use_mysql.jpg](https://upload-images.jianshu.io/upload_images/12273007-4f374bfdf093883e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





