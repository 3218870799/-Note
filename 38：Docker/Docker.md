## 1、简介

**Docker**是一个开源的应用容器引擎；是一个轻量级容器技术；

Docker支持将软件编译成一个镜像；然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像；

运行中的这个镜像称为容器，容器启动是非常快速的。

![](media/搜狗截图20180303145450.png)



![](media/搜狗截图20180303145531.png)

基本工作流程：

Docker使用客户端-服务器架构。Docker *客户端*与Docker *守护进程*进行对话，该*守护进程*完成了构建，运行和分发Docker容器的繁重工作。Docker客户端和守护程序*可以* 在同一系统上运行，也可以将Docker客户端连接到远程Docker守护程序。Docker客户端和守护程序在网络接口上使用REST API进行通信。

**Docker守护程序（Docker daemon）**

Docker守护程序侦听Docker API请求并管理Docker对象，例如图像，容器，网络和卷。守护程序还可以与其他守护程序通信以管理Docker服务。

**Docker客户端（Client）**

Docker客户端是Docker用户与Docker交互的主要方式。当您使用诸如`docker run`之类的命令时，客户端会将这些命令发送到`Docker daemon`，以执行这些命令。Docker客户端可以与多个守护程序通信。

**Docker注册中心（Registry）**

Docker *注册中心*存储Docker镜像。Docker Hub是任何人都可以使用的公共注册中心（远程/中央仓库），并且Docker默认配置为在Docker Hub上查找映像。

使用`docker pull`或`docker run`命令时，所需的镜像将从配置的中央仓库中提取。使用`docker push`命令时，会将镜像推送到配置的注册中心。



## 2、核心概念

docker主机(Host)：安装了Docker程序的机器（Docker直接安装在操作系统之上）；

docker客户端(Client)：连接docker主机进行操作；

docker仓库(Registry)：用来保存各种打包好的软件镜像；

docker镜像(Images)：软件打包好的镜像；放在docker仓库中；

docker容器(Container)：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用

![](media/搜狗截图20180303165113.png)

使用Docker的步骤：

1）、安装Docker

2）、去Docker仓库找到这个软件对应的镜像；

3）、使用Docker运行这个镜像，这个镜像就会生成一个Docker容器；

4）、对容器的启动停止就是对软件的启动停止；







## 3、安装Docker

#### 1）、安装linux虚拟机

​	1）、VMWare、VirtualBox（安装）；

​	2）、导入虚拟机文件centos7-atguigu.ova；

​	3）、双击启动linux虚拟机;使用  root/ 123456登陆

​	4）、使用客户端连接linux服务器进行命令操作；

​	5）、设置虚拟机网络；

​		桥接网络===选好网卡====接入网线；

​	6）、设置好网络以后使用命令重启虚拟机的网络

```shell
service network restart
```

​	7）、查看linux的ip地址

```shell
ip addr
```

​	8）、使用客户端连接linux；

#### 2）、在linux虚拟机上安装docker

步骤：

```shell
1、检查内核版本，必须是3.10及以上
uname -r
2、安装docker
yum install docker
3、输入y确认安装
4、启动docker
[root@localhost ~]# systemctl start docker
[root@localhost ~]# docker -v
Docker version 1.12.6, build 3e8e77d/1.12.6
5、开机启动docker
[root@localhost ~]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
6、停止docker
systemctl stop docker
```

## 4、Docker常用命令&操作

### 1）、镜像操作

| 操作 | 命令                                            | 说明                                                     |
| ---- | ----------------------------------------------- | -------------------------------------------------------- |
| 检索 | docker  search 关键字  eg：docker  search redis | 我们经常去docker  hub上检索镜像的详细信息，如镜像的TAG。 |
| 拉取 | docker pull 镜像名:tag                          | :tag是可选的，tag表示标签，多为软件的版本，默认是latest  |
| 列表 | docker images                                   | 查看所有本地镜像                                         |
| 删除 | docker rmi image-id                             | 删除指定的本地镜像                                       |

https://hub.docker.com/

根据镜像启动容器：

```cmd
docker run --name mytomcat -d tomcat:latest
```

参数说明：

![image-20201228220330904](media/image-20201228220330904.png)

**获取镜像**

根据镜像名称拉取镜像

```
[root@docker01 ~]# docker pull centos
Using default tag: latest
latest: Pulling from library/centos
af4b0a2388c6: Downloading  34.65MB/73.67MB
```

查看当前主机镜像列表

```
[root@docker01 ~]# docker image list 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              ff426288ea90        3 weeks ago         207MB
nginx               latest              3f8a4339aadd        5 weeks ago         108MB
```

拉第三方镜像方法

```
docker pull index.tenxcloud.com/tenxcloud/httpd
```

**导出镜像**

```
[root@docker01 ~]# docker image list 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              ff426288ea90        3 weeks ago         207MB
nginx               latest              3f8a4339aadd        5 weeks ago         108MB
# 导出
[root@docker01 ~]# docker image save centos > docker-centos.tar.gz
```

**删除镜像**

```
[root@docker01 ~]# docker image rm centos:latest
[root@docker01 ~]# docker image list 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              3f8a4339aadd        5 weeks ago         108MB
```



**导入镜像**

```
[root@docker01 ~]# docker image load -i docker-centos.tar.gz  
e15afa4858b6: Loading layer  215.8MB/215.8MB
Loaded image: centos:latest
[root@docker01 ~]# docker image list 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              ff426288ea90        3 weeks ago         207MB
nginx               latest              3f8a4339aadd        5 weeks ago         108MB
```



**查看镜像的详细信息**

```
[root@docker01 ~]# docker image inspect centos
```



### 2）、容器操作

软件镜像（QQ安装程序）----运行镜像----产生一个容器（正在运行的软件，运行的QQ）；

步骤：

````shell
1、搜索镜像
[root@localhost ~]# docker search tomcat
2、拉取镜像
[root@localhost ~]# docker pull tomcat
3、根据镜像启动容器
docker run --name mytomcat -d tomcat:latest
4、docker ps  
查看运行中的容器
5、 停止运行中的容器
docker stop  容器的id
6、查看所有的容器
docker ps -a
7、启动容器
docker start 容器id
8、删除一个容器
 docker rm 容器id
9、启动一个做了端口映射的tomcat
[root@localhost ~]# docker run -d -p 8888:8080 tomcat
-d：后台运行
-p: 将主机的端口映射到容器的一个端口    主机端口:容器内部的端口

10、为了演示简单关闭了linux的防火墙
service firewalld status ；查看防火墙状态
service firewalld stop：关闭防火墙
11、查看容器的日志
docker logs container-name/container-id

更多命令参看
https://docs.docker.com/engine/reference/commandline/docker/
可以参考每一个镜像的文档

````

查看正在运行的容器

```
[root@docker01 ~]# docker container ls
    或
[root@docker01 ~]# docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
8708e93fd767        nginx               "nginx -g 'daemon of…"   6 seconds ago       Up 4 seconds        80/tcp              keen_lewin
```





查看你容器详细信息/ip

```
[root@docker01 ~]# docker container  inspect  容器名称/id
```





查看你所有容器（包括未运行的）

```
[root@docker01 ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
8708e93fd767        nginx               "nginx -g 'daemon of…"   4 minutes ago       Exited (0) 59 seconds ago                       keen_lewin
f9f3e6af7508        nginx               "nginx -g 'daemon of…"   5 minutes ago       Exited (0) 5 minutes ago                        optimistic_haibt
8d8f81da12b5        nginx               "nginx -g 'daemon of…"   3 hours ago         Exited (0) 3 hours ago                          lucid_bohr
```



停止容器

```
[root@docker01 ~]# docker stop 容器名称/id 
或
[root@docker01 ~]# docker container  kill  容器名称/id

```

**进入容器方法**

**启动时进去方法**

```
[root@docker01 ~]# docker run -it #参数：-it 可交互终端
[root@docker01 ~]# docker run -it nginx:latest  /bin/bash
root@79241093859e:/#
```



退出/离开容器

```
ctrl+p & ctrl+q
```



**启动后进入容器的方法**

启动一个docker

```
[root@docker01 ~]# docker run -it centos:latest 
[root@1bf0f43c4d2f /]# ps -ef 
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 15:47 pts/0    00:00:00 /bin/bash
root         13      1  0 15:47 pts/0    00:00:00 ps -ef
```



attach进入容器，使用pts/0 ，会让所用通过此方法进如放入用户看到同样的操作。

```
[root@docker01 ~]# docker attach 1bf0f43c4d2f
[root@1bf0f43c4d2f /]# ps -ef 
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 15:47 pts/0    00:00:00 /bin/bash
root         14      1  0 15:49 pts/0    00:00:00 ps -ef
```



自命名启动一个容器 --name

```
[root@docker01 ~]# docker attach 1bf0f43c4d2f
[root@1bf0f43c4d2f /]# ps -ef 
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 15:47 pts/0    00:00:00 /bin/bash
root         14      1  0 15:49 pts/0    00:00:00 ps -ef
```



exec 进入容器方法（推荐使用）

```
[root@docker01 ~]# docker exec -it clsn1  /bin/bash 
[root@b20fa75b4b40 /]# 重新分配一个终端
[root@b20fa75b4b40 /]# ps -ef 
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 16:11 pts/0    00:00:00 /bin/bash
root         13      0  0 16:14 pts/1    00:00:00 /bin/bash
root         26     13  0 16:14 pts/1    00:00:00 ps -ef
```

**删除所有容器**

```
[root@docker01 ~]# docker rm -f  `docker ps -a -q`
# -f 强制删除
```



**启动时进行端口映射**

-p参数端口映射

```
[root@docker01 ~]# docker run -d -p 8888:80  nginx:latest 
287bec5c60263166c03e1fc5b0b8262fe76507be3dfae4ce5cd2ee2d1e8a89a9
```





### 3）、安装MySQL示例

```shell
docker pull mysql
```

错误的启动

```shell
[root@localhost ~]# docker run --name mysql01 -d mysql
42f09819908bb72dd99ae19e792e0a5d03c48638421fa64cce5f8ba0f40f5846

mysql退出了
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS               NAMES
42f09819908b        mysql               "docker-entrypoint.sh"   34 seconds ago      Exited (1) 33 seconds ago                            mysql01
538bde63e500        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       compassionate_
goldstine
c4f1ac60b3fc        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       lonely_fermi
81ec743a5271        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       sick_ramanujan


//错误日志
[root@localhost ~]# docker logs 42f09819908b
error: database is uninitialized and password option is not specified 
  You need to specify one of MYSQL_ROOT_PASSWORD, MYSQL_ALLOW_EMPTY_PASSWORD and MYSQL_RANDOM_ROOT_PASSWORD；这个三个参数必须指定一个
```

正确的启动

```shell
[root@localhost ~]# docker run --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
b874c56bec49fb43024b3805ab51e9097da779f2f572c22c695305dedd684c5f
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
b874c56bec49        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 3 seconds        3306/tcp            mysql01
```

做了端口映射

```shell
[root@localhost ~]# docker run -p 3306:3306 --name mysql02 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
ad10e4bc5c6a0f61cbad43898de71d366117d120e39db651844c0e73863b9434
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
ad10e4bc5c6a        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 2 seconds        0.0.0.0:3306->3306/tcp   mysql02
```



几个其他的高级操作

```
docker run --name mysql03 -v /conf/mysql:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
把主机的/conf/mysql文件夹挂载到 mysqldocker容器的/etc/mysql/conf.d文件夹里面
改mysql的配置文件就只需要把mysql配置文件放在自定义的文件夹下（/conf/mysql）

docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
指定mysql的一些配置参数
```

