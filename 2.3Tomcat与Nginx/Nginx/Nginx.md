# Nginx 简介

## Nginx 发展介绍

Nginx （engine x）
是一个高性能的 Web 服务器和反向代理服务器，也可以作为邮件代理服务器。

Nginx
特点是占有内存少，并发处理能力强，以高性能、低系统资源消耗而闻名，Nginx 官方测试为 5 万并发请求。与 Nginx 同类型的 Web 服务器还有 Apache、Lighttpd（音同 lighty）、Tengine（阿里巴巴的）
等。Nginx
的并发处理能力在同类型的 Web 服务器中表现极好（Apache、Lighttpd），在全世界范围内大量的网站使用了 Nginx，国内互联网中也大量使用了 Nginx，比如：淘宝、新浪、搜狐、网易、美团等。

Nginx 是免费开源的，同时 Nginx 也有收费的商业版本，商业版本提供了性能优化、宕机等紧急问题处理等技术支持和服务。

## Nginx 作者

Nginx 是由俄罗斯人 Igor Sysoev
采用 C 语言开发编写的，第一个公开版本 0.1.0 发布于 2004 年 10 月 4 日。

![](media/1f6353a5c2c4a36087bbcd62d106b457.png)

> Igor Sysoev，Nginx 的创始人

Igor
Sysoev 出生于 1970 年的阿拉木图（哈萨克斯坦共和国城市），也就是前苏联。1991 年苏联解体，哈萨克斯坦宣布独立，Nginx 作者 1994 年毕业于莫斯科国立鲍曼技术大学；

毕业后继续在莫斯科工作和生活，就职于 NGINX.Inc，任 CTO。<https://www.nginx.com/>

## 正向代理和反向代理概念

反向代理（Reverse
Proxy）方式是指以代理服务器来接受 internet 上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给 internet 上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器；

正向代理类似一个跳板机，代理访问外部资源。比如：我是一个用户，我访问不了某网站，但是我能访问一个代理服务器，这个代理服务器，它能访问那个我不能访问的网站，于是我先连上代理服务器，告诉它我需要那个无法访问网站的内容，代理服务器去取回来,然后返回给我。

## 正向代理和反向代理举例

### 正向代理

比如你现在缺钱，想找马云爸爸去借钱，可想而知人家可能鸟都不鸟你，到最后碰一鼻子灰借不到钱。不过你认识你家隔壁老王，而老王认识马云同志，而且关系还很好。这时候你托老王去找马云借钱，当然这事最后成了，你从马云那里借到了 500 万！这时候马云并不知道钱是你借的，只知道这钱是老王借的。最后由老王把钱转交给你。在这里，老王就充当了一个重要的角色：代理。

此时的代理，就是我们常说的正向代理。代理客户端去请求服务器，隐藏了真实客户端，服务器并不知道真实的客户端是谁。正向代理应用最广泛的莫过于现在的某些“科学上网工具”，你访问不了谷歌、Facebook 的时候，你可以在国外搭建一台代理服务器，代理你访问，代理服务器再把请求到的数据转交给你，你就可以看到内容了。

### 反向代理

比如你现在很无聊，想找人聊天，这时候你拨通了联通客服 10010 电话，联通的总机可能随机给你分配一个闲置的客服给你接通。这时候你如愿以偿的和客服聊了起来，问了问她目前有没有结婚、有没有对象、家住哪里、她的微信号、她的手机号。。。

此时联通总机充当的角色就是反向代理，你只知道和客服接通并聊了起来，具体为什么会接通这个客服 MM，怎么接通的，你并不知道。

反向代理隐藏了真正的服务端，就像你每天使用百度的时候，只知道敲打www.baidu.com就可以打开百度搜索页面，但背后成千上万台百度服务器具体是哪一台为我们服务的，我们并不知道。我们只知道这个代理服务器，它会把我们的请求转发到真实为我们服务的那台服务器那里去。

**综上所述：正向代理代理对象是客户端，反向代理代理对象是服务端。**

## 总结

软件层面一般常用 Nginx 来做反向代理服务器，它的性能非常好，用来做负载均衡。

# Nginx 环境搭建

## 下载

> 免费开源版的官方网站：<http://nginx.org>

> Nginx 有 Windows 版本和 Linux 版本，但更推荐在 Linux 下使用 Nginx；

> 下载 nginx-1.14.2.tar.gz 的源代码文件：wget
> <http://nginx.org/download/nginx-1.14.2.tar.gz>

> 我的习惯，将下载或者上传的安装文件放到/home/soft/目录下

## 安装

### 安装前的准备

Nginx 的安装需要确定 Linux 安装相关的几个库，否则配置和编译会出现错误，
具体的检查安装过程为：

#### gcc 编译器是否安装

检查是否安装：yum list installed \| grep gcc

执行安装：yum install gcc -y

#### openssl 库是否安装

检查是否安装：yum list installed \| grep openssl

执行安装：yum install openssl openssl-devel -y

#### pcre 库是否安装

检查是否安装：yum list installed \| grep pcre

执行安装：yum install pcre pcre-devel -y

#### zlib 库是否安装

检查是否安装：yum list installed \| grep zlib

执行安装：yum install zlib zlib-devel -y

#### 一次性安装，执行如下命令

yum install gcc openssl openssl-devel pcre pcre-devel zlib zlib-devel -y

### 正式安装

- 解压下载下来的 nginx 文件，执行命令：tar -zxvf nginx-1.14.2.tar.gz

- 切换至解压后的 nginx 主目录，执行命令：cd nginx-1.14.2

- 在 nginx 主目录 nginx-1.14.2 下执行命令：./configure --prefix=/usr/local/nginx

- （其中--prefix 是指定 nginx 安装路径） 注意:等号左右不要有空格

- 执行命令进行编译：make

- 执行命令进行安装：make install

  安装成功后，可以切换到/usr/local/nginx 目录下，查看内容

  ![](media/b053e41810942bdf06631857f50a54bb.png)

## 启动

### 普通启动

切换到 nginx 安装目录的 sbin 目录下，执行：./nginx

![](media/1524e022c7ae92ea5d618f12b7d63d7f.png)

### 通过配置文件启动

./nginx -c /usr/local/nginx/conf/nginx.conf

/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf

**其中-c 是指定配置文件,而且配置文件路径必须指定绝对路径**

![](media/39ca676b1c9c72333e42f9236c7eadc6.png)

### 检查 Nginx 是否启动

通过查看进程：ps -ef \| grep nginx

![](media/172b068c6a8578aaa9c3bcecb9913e16.png)

nginx 体系结构由 master 进程和其 worker 进程组成

master 进程读取配置文件，并维护 worker 进程，而 worker 进程则对请求进行实际处理

Nginx 启动后，安装目录下会出现一些\_tmp 结尾的文件，这些是临时文件，不用管。

在浏览器中输入<http://192.168.235.128:80/>访问 Nginx 服务器，出现以下界面

![](media/c7cb5ba28f6e429def7f5266f8bcd8ed.png)

## 关闭

### 优雅关闭 Nginx

找出 nginx 的进程号：ps -ef \| grep nginx

执行命令：kill -QUIT 主 pid

![](media/e775271d7ab803d8f3782af6fab073d4.png)

**注意：**

- 其中 pid 是主进程号的 pid（master process），其他为子进程 pid（worker process）

- 这种关闭方式会处理完请求后再关闭，所以称之为优雅的关闭

### 快速关闭 Nginx：

找出 nginx 的进程号：ps -ef \| grep nginx

kill -TERM 主 pid

**注意：**

- 其中 pid 是主进程号的 pid（master process），其他为子进程 pid（worker process）

- 这种关闭方式不管请求是否处理完成，直接关闭，比较暴力，称之为快速的关闭

  ![](media/95719982ec34c6cfff60967b7082ec62.png)

### 重启 Nginx：

./nginx -s reload

## 配置检查

当修改 Nginx 配置文件后，可以使用 Nginx 命令进行配置文件语法检查，用于检查 Nginx 配置文件是否正确

/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf –t

![](media/5f1ea066b88872bef25fad6975c96a8c.png)

## 其它

> Linux 上查看 nginx 版本：/usr/local/nginx/sbin/nginx -V

> \-v （小写的 v）显示 nginx 的版本

> \-V （大写的 V）显示 nginx 的版本、编译器版本和配置参数

![](media/37d2208a17113c944e4ba210ebc3d686.png)

## Window 下环境搭建（了解）

> 在官方网站下载最新 windows 版的 nginx：http://nginx.org/en/download.html

> 将下载下来的 nginx 压缩包解压到一个目录下，解压后该软件就可以启动使用了

> 启动方式 1：双击解压目录下的 nginx.exe 文件即可运行 nginx；

> 启动方式 2：进入 dos 窗口，切换到 nginx 主目录下，在 dos 窗口执行命令：start nginx

> 关闭方式 1：在资源管理器杀掉 Nginx 进程（有两个进程）

> 关闭方式 2：在 dos 窗口切换到 Nginx 安装主目录下执行命令：nginx -s stop

# Nginx 配置文件说明及 Nginx 主要应用

## Nginx 的核心配置文件

学习 Nginx 首先需要对它的核心配置文件有一定的认识，这个文件位于 Nginx 的安装目录/usr/local/nginx/conf 目录下，名字为 nginx.conf

![](media/0b1ff5f8f03b5d58eef964aa6da1ab8e.png)

> 详细配置，可以参考 resources 目录下的\<\<nginx 配置中文详解.conf\>\>

**Nginx 的核心配置文件主要由三个部分构成**

### 基本配置

![](media/58eae0fc52b25a413c5e05c4f562db0c.png)

### events 配置

![](media/9628270df44b7aeb04517c1f8b45289a.png)

### http 配置

#### 基本配置

![](media/4d6dfa8885acbd0a13e99ca93e222530.png)

#### server 配置，可以有多个

![](media/62c9903034ca529c51959276e0c4faa3.png)

## Nginx 主要应用

- 静态网站部署

  - 负载均衡

  - 静态代理

  - 动静分离

  - 虚拟主机

    后面的章节主要围绕这几个应用进行展开

# 静态网站部署

Nginx 是一个 HTTP 的 web 服务器，可以将服务器上的静态文件（如 HTML、图片等）通过 HTTP 协议返回给浏览器客户端

## 案例：将 ace-master 这个静态网站部署到 Nginx 服务器上

### 通过 Xftp 将 ace-master 到 linux 服务器/opt/static 目录下，为了演示方便，将名字改为 ace

![](media/3b3acd104678b446890d1bdca08c8c96.png)

### 通过<http://192.168.235.128:80/>访问

#### 修改 nginx.conf 配置文件

> 在 server 中，通过 location 匹配访问的路径，然后转发给静态资源

![](media/fec8ba092ffdb8cb21a1d83069337563.png)

> 注意要以分号结尾

#### 重启 nginx 服务器

![](media/a176b44036e4f504d71cdf0dfa9ac3b3.png)

#### 在浏览器中输入<http://192.168.235.128:80/>进行访问

![](media/bc936d7270ad606234f0fcd020026800.png)

### 通过<http://192.168.235.128:80/ace/>访问

#### 修改 nginx.conf 配置文件

> 在 server 中，通过 location 匹配访问的路径，然后转发给静态资源

![](media/a6ea9713a5c1b77985f9947774af0aa7.png)

#### 重启 nginx 服务器

![](media/ad4e9bf21f6e0680008cfcbcac7282ee.png)

#### 在浏览器中输入<http://192.168.235.128:80/>ace 进行访问

![](media/bc936d7270ad606234f0fcd020026800.png)

## location 中配置路径讲解

初次接触：可能会遇到 404 找不到页面的错误，主要原因是配置路径问题；

**规则：ip + port 等于 root，假设 server 的配置如下：**

> server {

> listen 80; \#端口号

> location / {

> root /opt/static /ace; \#静态文件路径

> }

> }

> **替换：**

http://192.168.92.128:80/ = root = /opt/static/ace

http://192.168.92.128:80/ace = root/ace = /opt/static/ace/ace

| **location 匹配顺序** 在没有标识符的请求下，匹配规则如下： 1、nginx 服务器首先在 server 块的多个 location 块中搜索是否有标准的 uri 和请求字符串匹配。如果有多个标准 uri 可以匹配，就匹配其中匹配度最高的一个 location。 2、然后，nginx 在使用 location 块中，正则 uri 和请求字符串，进行匹配。如果正则匹配成功，则结束匹配，并使用这个 location 处理请求；如果正则匹配失败，则使用标准 uri 中，匹配度最高的 location。 **备注：** 1、如果有精确匹配，会先进行精确匹配，匹配成功，立刻返回结果。 2、普通匹配与顺序无关，因为按照匹配的长短来取匹配结果。 3、正则匹配与顺序有关，因为是从上往下匹配。(首先匹配，就结束解析过程) 4、在 location 中，有一种统配的 location，所有的请求，都可以匹配，如下： location / { \# 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求 \# 但是正则和最长字符串会优先匹配 }

结合标识符，匹配顺序如下：

(location =) \> (location 完整路径) \> (location \^\~ 路径) \> (location \~,\~\*
正则顺序) \> (location 部分起始路径) \> (location /)

即

（精确匹配）\> (最长字符串匹配，但完全匹配)
\>（非正则匹配）\>（正则匹配）\>（最长字符串匹配，不完全匹配）\>（location 通配）

# 负载均衡

## 负载均衡概述

在网站创立初期，我们一般都使用单台机器对外提供集中式服务。随着业务量的增大，我们一台服务器不够用，此时就会把多台机器组成一个集群对外提供服务，但是，我们网站对外提供的访问入口通常只有一个，比如
www.web.com。那么当用户在浏览器输入www.web.com进行访问的时候，如何将用户的请求分发到集群中不同的机器上呢，这就是负载均衡要做的事情。

负载均衡通常是指将请求"均匀"分摊到集群中多个服务器节点上执行，这里的均匀是指在一个比较大的统计范围内是基本均匀的，并不是完全均匀。

## 负载均衡实现方式

### 硬件负载均衡

比如 F5、深信服、Array 等

优点是有厂商专业的技术服务团队提供支持，性能稳定

缺点是费用昂贵，对于规模较小的网络应用成本太高

### 软件负载均衡

比如 Nginx、LVS、HAProxy 等

优点是免费开源，成本低廉

### Nginx 负载均衡

> Nginx 通过在 nginx.conf 文件进行配置即可实现负载均衡

#### 原理图

![](media/fd9e76d5b262798b76b3717a4fef3474.png)

#### 配置如下：（配置 2 步即可）

##### 在 http 模块加上 upstream 配置

> upstream www.myweb.com {

> server 127.0.0.1:9100 weight=3;

> server 127.0.0.1:9200 weight=1;

> }

其中 weight=1 表示权重，用于后端服务器性能不均的情况，访问比率约等于权重之比，权重越大访问机会越多

**upstream 是配置 nginx 与后端服务器负载均衡非常重要的一个模块**，并且它还能对后端的服务器的健康状态进行检查，若后端服务器中的一台发生故障，则前端的请求不会转发到该故障的机器

##### 在 server 模块里添加 location，并配置 proxy_pass

> location /myweb {

> proxy_pass http://www.myweb.com;

> }

> 其中 www.myweb.com 字符串要和 upstream 后面的字符串相等

#### 案例：将 resources/site 中的 myweb.war 部署到两台不同的 tomcat 上，通过 nginx 实现负载均衡

##### 在 linux 服务器/usr/local 目录下，拷贝两台新的 tomcat

![](media/d71d16917a92302749b4e64959d62b0a.png)

##### 将这两台 tomcat 服务器 webapps 目录下没用的项目删掉

![](media/7e301618dca3a16f59dd1b6b56fe4017.png)

![](media/3a42b0fa79e262895892dfefa6515331.png)

##### 修改其中一台 tomcat9100 的端口号为 9100

![](media/bdbf49ac3deee38f3980c8e51a7bf702.png)

##### 修改 tomcat9200 的端口号为 9200 ，因为需要同时启动两台，这里其它的端口号也要修改

![](media/180c1a07b067d156014f6d68bd85a8a6.png)

![](media/fcd0b1d8ec9002480d8c8704a32068b9.png)

![](media/12198ba4feb186e4e0bb31eddea0eda6.png)

##### 将 myweb.war 上传到两台 tomcat 服务器的 webapps 目录下

![](media/2375dd20ec9c07bb0025a67010aaf3fb.png)

![](media/e79b54e5e05653eb0efca2e904c94e9a.png)

##### 启动两台 tomcat

![](media/f4a3f592b96f1db8db55f592593054c2.png)

![](media/e64607e49f238fd07d45a3fc918145c5.png)

##### 浏览器直接访问两台 tomcat，进行测试

![](media/e70eecf1c119653e5910fae759408e99.png)

![](media/44b46cf56130eb9bfde7d7c13af1b8c7.png)

> 但是我们网站一般对外之后提供一个入口地址，所以这个时候可以使用 nginx 进行负载。

##### 配置 nginx

- 在 http 模块上加

  ![](media/0c754917c9a125b753e2201037bc11e1.png)

- 在 server 模块加

  ![](media/c4b959897cd1256ac8d0a214384b7d1f.png)

##### 重启 Nginx

![](media/54c2778fa2c4e22eafb00eedfd2f7560.png)

##### 浏览器直接访问 nginx 进行测试

- 为了看到效果，在 tomcat9100 的 index.jsp 中加一个标记

  ![](media/5e841586898a44965a08d577b8add7ac.png)

- 这个时候可以看到，服务器的端口为 80，是 nginx 服务器的默认端口

  ![](media/f1e7311be6b3d52e5964637e61b95c91.png)

- 多访问几次，如果没有端口号，说明是 tomcat9200 被访问了

  ![](media/ae19ccf0a971c9d2b395f0d3451fb4b3.png)

## Nginx 常用负载均衡策略

### 轮询（默认）

**注意：这里的轮询并不是每个请求轮流分配到不同的后端服务器，与 ip_hash 类似，但是按照访问 url 的 hash 结果来分配请求，使得每个 url 定向到同一个后端服务器，主要应用于后端服务器为缓存时的场景下**。**如果后端服务器 down 掉，将自动剔除**

upstream backserver {

server 127.0.0.1:8080;

server 127.0.0.1:9090;

}

### 权重

**每个请求按一定比例分发到不同的后端服务器，weight 值越大访问的比例越大，用于后端服务器性能不均的情况**

upstream backserver {

server 192.168.0.14 weight=5;

server 192.168.0.15 weight=2;

}

### ip_hash

**ip_hash 也叫 IP 绑定，每个请求按访问 ip 的 hash 值分配，这样每个访问客户端会固定访问一个后端服务器，可以解决会话 Session 丢失的问题**

**算法：hash("124.207.55.82") % 2 = 0, 1**

upstream backserver {

ip_hash;

server 127.0.0.1:8080;

server 127.0.0.1:9090;

}

### 最少连接

**web 请求会被转发到连接数最少的服务器上**

upstream backserver {

least_conn;

server 127.0.0.1:8080;

server 127.0.0.1:9090;

}

### 案例：将负载均衡策略修改为 ip_hash 进行测试

#### 修改 nginx.conf 配置文件

![](media/d129fbe292fcf209286a55a3ba24ba7e.png)

#### 重启 nginx

![](media/2d2fe19c81192f44fb048bdc4f0a465a.png)

#### 浏览器访问测试，始终访问的是同一台 tomcat 服务器

不管刷新多少遍，始终访问的是同一台 tomcat 服务器

![](media/297a07cbfe0ba00ac2c8c0c7646a0637.png)

## 负载均衡其他几个配置

> **配置 1：**

upstream backserver {

server 127.0.0.1:9100;

\#其它所有的非 backup 机器 down 的时候，才请求 backup 机器

server 127.0.0.1:9200 backup;

}

> **配置 2：**

upstream backserver {

> server 127.0.0.1:9100;

> \#down 表示当前的 server 是 down 状态，不参与负载均衡

server 127.0.0.1:9200 down;

}

一般在项目上线的时候，可以分配部署不同的服务器上，然后对 Nginx 重新 reload。

reload 不会影响用户的访问，或者可以给一个提示页面,系统正在升级...

# 静态代理

把所有静态资源的访问改为访问 nginx，而不是访问 tomcat，这种方式叫静态代理。因为 nginx 更擅长于静态资源的处理，性能更好，效率更高。

所以在实际应用中，我们将静态资源比如图片、css、html、js 等交给 nginx 处理，而不是由 tomcat 处理。

![](media/d33b8da89e21c09c5d792bc0aaa21257.png)

## Nginx 静态代理实现方式

### 方式一 在 nginx.conf 的 location 中配置静态资源的后缀

**例如：当访问静态资源，则从 linux 服务器/opt/static 目录下获取（举例）**

location \~
.\*\\.(js\|css\|htm\|html\|gif\|jpg\|jpeg\|png\|bmp\|swf\|ioc\|rar\|zip\|txt\|flv\|mid

> \|doc\|ppt\|pdf\|xls\|mp3\|wma)\$ {

root /opt/static;

}

**说明**

- \~ 表示正则匹配，也就是说后面的内容可以是正则表达式匹配

- 第一个点 . 表示任意字符

- \*表示一个或多个字符

- \\. 是转移字符，是后面这个点的转移字符

- \| 表示或者

- \$ 表示结尾

整个配置表示以 .后面括号里面的这些后缀结尾的文件都由 nginx 处理

放置静态资源的目录，要注意一下目录权限问题，如果权限不足，给目录赋予权限；

否则会出现 403 错误 chmod 755

### 方式二 在 nginx.conf 的 location 中配置静态资源所在目录实现

**例如：当访问静态资源，则从 linux 服务器/opt/static 目录下获取（举例）**

location \~ .\*/(css\|js\|img\|images) {

root /opt/static;

}

xxx/css

xxx/js

xxx/img

xxx/images

我们将静态资源放入 /opt/static 目录下，然后用户访问时由 nginx 返回这些静态资源

### 案例： 通过 nginx 访问上面 myweb 案例中的图片

- 修改 nginx.conf 文件，在 location 中配置对静态资源的拦截，如果是静态资源，就交给 nginx 处理，使拦截静态文件后缀名的方式

  ![](media/f3a748c5824dc69a15e580148e8f59e6.png)

- 重启 nginx

  ![](media/4b0d4034d1e4114dc566e021667a96f4.png)

- 浏览器访问 nginx 服务器，进行测试，发现图片无法加载

  ![](media/ad29f185b03674947df761a97c1c4821.png)

- 右键图片，查看图片的地址为http://192.168.235.128/myweb/image/001.jpg，因为我们在nginx中配置了对.jpg的拦截，所以请求会交给nginx服务器进行处理。

  根据 ip +
  port 等于 root 的原则，我们会去/opt/static/myweb 目录下找资源，所以在/opt/static 下创建 myweb 目录，并放入 image 目录及图片，为了和 tomcat 上的图片区分，我们这里图片的内容发生了变化

  ![](media/9c5e6b8abbbd7ae5be0f8d460812a4d8.png)

- 浏览器访问 nginx 访问器，进行测试 ，图片访问正常，而且访问的是 nginx 上的图片

  ![](media/2c9c42b084b37e41781fe586cad18730.png)

- 将 nginx.conf 中的 location 拦截方式修改为拦截静态文件目录的方式

  ![](media/a9e654856052ff2dc97d50596d4fb70e.png)

- 重启 nginx 服务器，通过浏览器访问 nginx 服务器，进行测试，访问正常，但是访问的是 tomcat 服务器下的图片

  ![](media/4e10a1d717a4dc582a3ec269b13a9228.png)

  **注意：**

  我们拦截的目录为/images，

  请求的图片目录是/image，这样拦截不到；

  但是反过来

  我们拦截的目录为/imag

  请求的图片目录为/image，这样可以拦截到

所有我们需要将 location 的/images 修改为/image。所以一般我们用方式一，匹配静态资源后缀的方式更好一下，防止 js 拦截 jsp 目录。

# 动静分离

Nginx 的负载均衡和静态代理结合在一起，我们可以实现动静分离，这是实际应用中常见的一种场景。

动态资源，如 jsp 由 tomcat 或其他 web 服务器完成

静态资源，如图片、css、js 等由 nginx 服务器完成

它们各司其职，专注于做自己擅长的事情

动静分离充分利用了它们各自的优势，从而达到更高效合理的架构

## 动静分离案例

### 架构图

![](media/1a64762be008a35adeb56153959d4d14.png)

> 整个架构中，一个 nginx 负责负载均衡，两个 nginx 负责静态代理。Nginx 在一台 Linux 上安装一份，可以启动多个 Nginx，每个 Nginx 的配置文件不一样即可

### 实现步骤

#### 拷贝两份 nginx 配置文件（静态代理）

![](media/d4f55ba65c14035e7c56740d03a371b3.png)

#### 修改新拷贝的 nginx81.conf 和 nginx82.conf 配置文件

- Nginx81.conf 端口号，因为这两个机器只需要做静态代理，所以删除掉负载均衡的配置

  ![](media/659a33e7c832dc3e21e73f5a7b5f0c30.png)

- Nginx82.conf 端口号，因为这两个机器只需要做静态代理，所以删除掉负载均衡的配置

  ![](media/5d7c881b731723e968730185bc55fab2.png)

- 静态代理的配置

  ![](media/25a9e4f3bc6bfddba1f0b78b8b65108e.png)

#### 负载均衡 Nginx 配置（nginx.conf）

##### 动态资源的负载均衡

> upstream www.myweb.com {

> server 127.0.0.1:9100 weight=5;

> server 127.0.0.1:9200 weight=2;

> }

location /myweb {

proxy_pass http://www.myweb.com;

}

![](media/63b65df113260a5129b6866dca7a90af.png)

![](media/21a35134058547d71f6ea0cce8fe8981.png)

##### 静态资源的负载均衡

> upstream static.myweb.com {

> server 127.0.0.1:81 weight=1;

> server 127.0.0.1:82 weight=1;

> }

location \~ .\*/(css\|js\|img\|images) {

proxy_pass http://static.myweb.com;

}

![](media/7e073aa57795bb7a4b6f93fe0d1f06db.png)

![](media/1dad2a9f1522c82745de29bcf4cb63ea.png)

#### 启动三台 nginx 服务器，启动两台 tomcat 服务器

![](media/03853308567104a1bfb2cc1069c4905f.png)

#### 浏览器输入http://192.168.235.128/myweb/进行测试

> 关闭掉一台 nginx 静态代理服务器

> 关闭掉一台 tomcat 服务器

# 虚拟主机

虚拟主机，就是把一台物理服务器划分成多个“虚拟”的服务器，这样我们的一台物理服务器就可以当做多个服务器来使用，从而可以配置多个网站。

Nginx 提供虚拟主机的功能，就是为了让我们不需要安装多个 Nginx，就可以运行多个域名不同的网站。

Nginx 下，一个 server 标签就是一个虚拟主机。nginx 的虚拟主机就是通过 nginx.conf 中 server 节点指定的，想要设置多个虚拟主机，配置多个 server 节点即可；

例如：[www.meituan.com](http://www.meituan.com)
切换城市，可以看到不同的城市地址不一样（二级域名）

比如一个公司有多个二级域名，没有必要为每个二级域名都提供一台 Nginx 服务器，就可以使用虚拟主机技术，在一台 nginx 服务器上，模拟多个虚拟服务器。

## 配置虚拟主机方式

### 基于端口的虚拟主机（了解）

基于端口的虚拟主机配置，使用端口来区分

浏览器使用 同一个域名+端口 或 同一个 ip 地址+端口访问；

**server {**

> **listen 8080;**

**server_name www.myweb.com;**

**location /myweb {**

**proxy_pass http://www.myweb.com;**

**}**

> **}**

**server {**

**listen 9090;**

**server_name www.myweb.com;**

**location /p2p {**

**proxy_pass http://www.p2p.com;**

**}**

> **}**

### 基于域名的虚拟主机（掌握）

基于域名的虚拟主机是最常见的一种虚拟主机

**server {**

**listen 80;**

**server_name www.myweb.com;**

**location /myweb {**

**proxy_pass http://www. myweb.com;**

**}**

**}**

**server {**

**listen 80;**

**server_name www.p2p.com;**

**location /myweb {**

**proxy_pass http://www.p2p.com;**

**}**

**}**

需要修改一下本地的 hosts 文件，文件位置：C:\\Windows\\System32\\drivers\\etc\\hosts

在 hosts 文件配置：

> 192.168.208.128 www.myweb.com

> 192.168.208.128 www.p2p.com

前面是 Linux 的 IP，后面是你自定义的域名

## 虚拟主机案例，模拟城市站点网站（我们配置三个城市站点）

### 架构图

![](media/2dcc66a515ae5d7ec9d012d1980d7870.png)

### 实现步骤

#### 配置 3 个 Tomcat，每个 Tomcat 一个站点项目

- 部署在 Tomcat 的 ROOT 目录下，目的是访问的时候不用加上下文根，测试方便

- 拷贝一个新的 tomcat9300

  ![](media/5e325d6f8d5d6a4e94f61e3d906cce4c.png)

- 修改 tomcat9300 的端口号（修改 server.xml 文件）

  ![](media/fe10e6d26d918c366f1da8ee0dd443d8.png)

  ![](media/6681f6ca06c3bd83280b19d6f915c721.png)

  ![](media/507988b3bae1046b5c31a9e9adfa73e5.png)

- 在 Xshell 中开启三个选项卡，分别操作三个 tomcat

  ![](media/d3e93e5b47b5639904023a3e23fadc96.png)

- 清空三个 tomcat 的 webapps/ROOT 目录

  ![](media/a2840c7b2f95eff66ebea55f50b51ae3.png)

  ![](media/e94b31645dc1138a0a0847fd9446f707.png)

  ![](media/1a7d40834b8e36b82cca8f56a67663d3.png)

- 使用 Xftp 将三个 war 包上传到三个 tomcat 的 ROOT 目录

  beijing.war 上传到 tomcat9100 服务器上

  ![](media/0f6fa72c00e426f9e77fc4a62fcaf4aa.png)

  nanjing.war 上传到 tomcat9200 服务器上

  ![](media/efe3924b868d09851c35c5327c2b39d9.png)

  tianjin.war 上传到 tomcat9300 服务器上

  ![](media/698dbc6d7a720dbd132aed4cab63b731.png)

- 在 tomcat 启动前，使用 unzip 命令，直接解压三个 war 包，否则 tomcat 启动的时候，自动解压，还是有一个上下文目录的

  ![](media/299b84277c54275f3883999ada8ce181.png)

  在 tomcat9200 和 9300 上执行相同的操作

- 启动三个 tomcat

  ![](media/0b58a249d5ac34186fdc3e580e6242e7.png)

- 在浏览器中直接访问 tomcat 进行测试

  ![](media/a5ec9a76db7188f7eb57773d47f2db28.png)

  ![](media/81f41d8956d373d4f09320b6a10b8b99.png)

  ![](media/5026bbc79dd6aa3b02254af96339624c.png)

#### 在 nginx.conf 中配置 3 个 Nginx 虚拟主机

##### 方式一：直接在 nginx.conf 中配置

- 在 nginx.conf 文件添加三个 server 节点，用于配置三个虚拟主机

**server {**

**listen 80;**

**server_name beijing.myweb.com;**

**location / {**

**proxy_pass http://beijing.myweb.com;**

**}**

> **}**

**server {**

**listen 80;**

**server_name nanjing.myweb.com;**

**location / {**

**proxy_pass http://nanjing.myweb.com;**

**}**

> **}**

**server {**

**listen 80;**

**server_name tianjin.myweb.com;**

**location / {**

**proxy_pass http://tianjin.myweb.com;**

**}**

> **}**

![](media/60f65cfb4f5324ca4f82be902907d3db.png)

##### 方式二：通过单独的配置文件配置虚拟主机

通过 include 的方式引入虚拟主机配置

include /usr/local/nginx/conf/vhost/vhost.conf;

将虚拟目录的配置文件加入到”http {}”部分的末尾，与其他 server 并列；

文件是隔开的，配置更清晰，主文件没有那么多是 server

#### 在 nginx.conf 中配置每个虚拟主机请求转发所对应的后端服务器（负载均衡，可以配置多个服务器）

**upstream beijing.myweb.com {**

**server 127.0.0.1:9100;**

**}**

**upstream nanjing.myweb.com {**

**server 127.0.0.1:9200;**

**}**

**upstream tianjin.myweb.com {**

**server 127.0.0.1:9300;**

**}**

![](media/179ceafcea3d31344852104e3015ed8d.png)

#### **修改 hosts 文件，让 Linux 的 ip 指向到三个站点的域名**

C:\\Windows\\System32\\drivers\\etc\\hosts

192.168.235.128 beijing.myweb.com

192.168.235.128 nanjing.myweb.com

192.168.235.128 tianjin.myweb.com

![](media/b1f7a6e2ab160cfc5da5a92d077ef0a9.png)

#### 重启 nginx，通过浏览器访问 nginx 进行测试

![](media/d2f94a6261aa44c6ed105d60b8eb4af6.png)

![](media/5f351a101f32a301181ac50236d2aa20.png)

![](media/1bc8b31d7b1c9260fd03a84b04075c78.png)

![](media/c780a515a39cec3a318b0a746b54c68b.png)

#### 通过单独的配置文件配置虚拟主机，然后再 nginx.conf 中引入（参照步骤 2 的方式二）

- 在/usr/local/nginx/conf 目录下，创建 vhost 目录，并创建 vhost.conf 文件，在其中配置虚拟主机信息

  ![](media/852011576f8de3d3eee6a7c524252301.png)

- 在 nginx.conf 中引入上面的配置文件

  ![](media/b0fa2e55a4d29eb1acf86153838c956f.png)

# Nginx 限流

## 限流算法

令牌桶算法

- 令牌以固定速率产生，并缓存到令牌桶中；
- 令牌桶放满时，多余的令牌被丢弃；
- 请求要消耗等比例的令牌才能被处理；
- 令牌不够时，请求被缓存。

漏桶算法：

- 水（请求）从上方倒入水桶，从水桶下方流出（被处理）；
- 来不及流出的水存在水桶中（缓冲），以固定速率流出；
- 水桶满后水溢出（丢弃）。
- 这个算法的核心是：缓存请求、匀速处理、多余的请求直接丢弃。

从作用上来说，漏桶和令牌桶算法最明显的区别就是是否允许突发流量(burst)的处理，漏桶算法能够强行限制数据的实时传输（处理）速率，对突发流量不做额外处理；而令牌桶算法能够在限制数据的平均传输速率的同时允许某种程度的突发传输。

Nginx 按请求速率限速模块使用的是漏桶算法，即能够强行保证请求的实时处理速度不会超过设置的阈值。

Nginx 官方版本限制 IP 的连接和并发分别有两个模块：

- `limit_req_zone` 用来限制单位时间内的请求数，即速率限制,采用的漏桶算法 "leaky bucket"。
- `limit_req_conn` 用来限制同一时间连接数，即并发限制。

```properties
limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
```

- 第一个参数：$binary*remote_addr 表示通过 remote_addr 这个标识来做限制，“binary*”的目的是缩写内存占用量，是限制同一客户端 ip 地址。
- 第二个参数：zone=one:10m 表示生成一个大小为 10M，名字为 one 的内存区域，用来存储访问的频次信息。
- 第三个参数：rate=1r/s 表示允许相同标识的客户端的访问频次，这里限制的是每秒 1 次，还可以有比如 30r/m 的。

```properties

```

处理突发量：burst 与 nodelay

**burst=20 **，若同时有 21 个请求到达，Nginx 会处理第一个请求，剩余 20 个请求将放入队列，然后每隔 100ms 从队列中获取一个请求进行处理。若请求数大于 21，将拒绝处理多余的请求，直接返回 503.

nodelay 针对的是 burst 参数，burst=20 nodelay 表示这 20 个请求立马处理，不能延迟，相当于特事特办。不过，即使这 20 个突发请求立马处理结束，后续来了请求也不会立马处理。burst=20 相当于缓存队列中占了 20 个坑，即使请求被处理了，这 20 个位置这只能按 100ms 一个来释放。

这就达到了速率稳定，但突然流量也能正常处理的效果。
