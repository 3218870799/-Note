# Session会话管理概述

## Web中的Session和Cookie回顾

### Session机制

由于HTTP协议是无状态的协议，一次浏览器和服务器的交互过程就是：

>   浏览器：你好吗？

>   服务器：很好！

这就是一次会话，对话完成后，这次会话就结束了，服务器端并不能记住这个人，下次再对话时，服务器端并不知道是上一次的这个人，所以服务端需要记录用户的状态时，就需要用某种机制来识别具体的用户，这个机制就是Session。

### Cookie

服务端如何识别特定的客户？

这个时候需要使用Cookie。每次HTTP请求的时候，客户端都会发送相应的Cookie信息到服务端。

实际上大多数的应用都是用 Cookie
来实现Session跟踪的，第一次创建Session时，服务端会在HTTP协议中向客户端 Cookie
中记录一个Session
ID，以后每次请求把这个会话ID发送到服务器，这样服务端就知道客户端是谁了。

### url重写

那么如果客户端的浏览器禁用了 Cookie 怎么办？

一般这种情况下，会使用一种叫做URL重写的技术来进行session会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如
sessionId=xxxxx 这样的参数，服务端据此来识别客户端是谁

## Session会话管理及带来的问题

在Web项目开发中，Session会话管理是一个很重要的部分，用于存储与记录用户的状态或相关的数据。

通常情况下session交由容器（tomcat）来负责存储和管理，但是如果项目部署在多台tomcat中，则session管理存在很大的问题

-   多台tomcat之间无法共享session，比如用户在tomcat
    A服务器上已经登录了，但当负载均衡跳转到tomcat B时，由于tomcat
    B服务器并没有用户的登录信息，session就失效了，用户就退出了登录

-   一旦tomcat容器关闭或重启也会导致session会话失效

因此如果项目部署在多台tomcat中，就需要解决session共享的问题

## Session会话共享方案

-   第一种是使用容器扩展插件来实现，比如基于Tomcat的tomcat-redis-session-manager插件，基于Jetty的jetty-session-redis插件、memcached-session-manager插件；这个方案的好处是对项目来说是透明的，无需改动代码，但是由于过于依赖容器，一旦容器升级或者更换意味着又得重新配置

    其实底层是，复制session到其它服务器，所以会有一定的延迟，也不能部署太多的服务器。

    -   第二种是使用Nginx负载均衡的ip_hash策略实现用户每次访问都绑定到同一台具体的后台tomcat服务器实现session总是存在

        这种方案的局限性是ip不能变，如果手机从北京跳到河北，那么ip会发生变化；另外负载均衡的时候，如果某一台服务器发生故障，那么会重新定位，也会跳转到别的机器。

    -   第三种是自己写一套Session会话管理的工具类，在需要使用会话的时候都从自己的工具类中获取，而工具类后端存储可以放到Redis中，这个方案灵活性很好，但开发需要一些额外的时间。

    -   第四种是使用框架的会话管理工具，也就是我们要介绍的Spring
        session，这个方案既不依赖tomcat容器，又不需要改动代码，由Spring
        session框架为我们提供，可以说是目前非常完美的session共享解决方案

# Spring Session入门

## Spring Session简介

>   Spring Session
>   是Spring家族中的一个子项目，它提供一组API和实现，用于管理用户的session信息

>   它把servlet容器实现的httpSession替换为spring-session，专注于解决
>   session管理问题，Session信息存储在Redis中，可简单快速且无缝的集成到我们的应用中；

>   官网：<https://spring.io/>

>   **Spring Session的特性**

-   提供用户session管理的API和实现

-   提供HttpSession，以中立的方式取代web容器的session，比如tomcat中的session

-   支持集群的session处理，不必绑定到具体的web容器去解决集群下的session共享问题

## 入门案例

### 环境配置

#### 创建一个空的Maven project，名字及路径根据自己的情况定。

![](media/903033560bf0b43b4616004cca6c55e1.png)

#### 空project创建好后，会提示创建模块，我们暂时先取消

![](media/bae5687420a58c502c473354187b91d2.png)

#### 设置字体

![](media/9c165329e2ff9cd5fc683386d747bead.png)

#### 设置编码方式

![](media/4e5a1509746f0ddcdee3117acc10ef60.png)

#### 设置maven信息

![](media/98139d6c459026238b7f4c19974e5c5b.png)

#### 创建一个Maven的web module，名字为01-springsession-web

![](media/1c3a6235b375cb5c8e490bc3d4f172b6.png)

![](media/ed25533fe437bcbb733af54cc087e20d.png)

#### 完善Maven项目的结构

##### 在main目录下，创建java目录，并标记为Sources Root

![](media/469d113edfc9b905e178d7f3414c2b7a.png)

##### 在main目录下，创建resources目录，并标记为Resources Root

![](media/6b4fc2d80b203f13ce0094c27bc0f0d5.png)

### 代码开发

#### 创建向session放数据的servlet

-   在java目录下创建包com.bjpowernode.session.servlet包

    ![](media/64aca200dc320570c18d25faa0d5019a.png)

-   在servlet包下创建SetSessionServlet

    ![](media/adb7ec3f111640c6779bc6968984d5f0.png)

    ![](media/fbd99e3d38ee8882d972d78e8af19d8f.png)

-   在Servlet中通过注解指定urlPatterns，并编写代码

    ![](media/429ffcf90dd87c7c5d0d68699b105457.png)

#### 创建从session放数据的servlet

-   在servlet包下创建GetSessionServlet

    ![](media/adb7ec3f111640c6779bc6968984d5f0.png)

    ![](media/007f129330d5349b1d79927e46aed308.png)

-   在Servlet中通过注解指定urlPatterns，并编写代码

    ![](media/185487af40a6064aa26f474729529cad.png)

#### 向pom.xml文件中添加servlet及jsp的配置

>   \<dependencies\>  
>   \<!-- servlet依赖的jar包start --\>  
>   \<dependency\>  
>   \<groupId\>javax.servlet\</groupId\>  
>   \<artifactId\>javax.servlet-api\</artifactId\>  
>   \<version\>3.1.0\</version\>  
>   \</dependency\>  
>   \<!-- servlet依赖的jar包start --\>  
>     
>   \<!-- jsp依赖jar包start --\>  
>   \<dependency\>  
>   \<groupId\>javax.servlet.jsp\</groupId\>  
>   \<artifactId\>javax.servlet.jsp-api\</artifactId\>  
>   \<version\>2.3.1\</version\>  
>   \</dependency\>  
>   \<!-- jsp依赖jar包end --\>  
>     
>   \<!--jstl标签依赖的jar包start --\>  
>   \<dependency\>  
>   \<groupId\>javax.servlet\</groupId\>  
>   \<artifactId\>jstl\</artifactId\>  
>   \<version\>1.2\</version\>  
>   \</dependency\>  
>   \<!--jstl标签依赖的jar包end --\>  
>   \</dependencies\>

#### 部署访问测试（目前无法实现session共享）

##### 配置tomcat9100服务器

-   打开Edit Configurations选项

    ![](media/34bce0373cd399c3a63a152a3fb5b180.png)

-   添加tomcat配置

    ![](media/98f60c34daaf3ca1132077ed7c67fe76.png)

-   给tomcat服务器取名，并修改端口号

    ![](media/7ac11fe8ea4fe591573936d43027b735.png)

-   将项目部署到tomcat9100上

    ![](media/eba2d9c8439c58e64d59bba9a1ea0575.png)

-   指定项目的上下文根为/01-springsession-web

    ![](media/e928df381ef35b0a833e9e1fcb4f60c0.png)

-   为了实现热部署，在Server选项卡中，配置以下两个选项

    ![](media/682ee55a1df1b4c05abcc48da9939d95.png)

##### 配置tomcat9200服务器

操作步骤同配置tomcat9100，配完之后在Application Servers窗口中如下

![](media/37f176a1ec37b6ccd62c0c2504c969cd.png)

### SpringSession集成配置

#### 在pom.xml文件中，添加Spring Session相关的依赖

>   \<!-- Spring session redis 依赖start --\>  
>   \<dependency\>  
>   \<groupId\>org.springframework.session\</groupId\>  
>   \<artifactId\>spring-session-data-redis\</artifactId\>  
>   \<version\>1.3.1.RELEASE\</version\>  
>   \</dependency\>  
>   \<!-- Spring session redis 依赖end --\>  
>   \<!-- spring web模块依赖 start --\>  
>   \<dependency\>  
>   \<groupId\>org.springframework\</groupId\>  
>   \<artifactId\>spring-web\</artifactId\>  
>   \<version\>4.3.16.RELEASE\</version\>  
>   \</dependency\>  
>   \<!-- spring web模块依赖end --\>

#### 在web.xml文件中配置springSessionRepositoryFilter过滤器

>   \<filter\>  
>   \<filter-name\>springSessionRepositoryFilter\</filter-name\>  
>   \<filter-class\>org.springframework.web.filter.DelegatingFilterProxy\</filter-class\>  
>   \</filter\>  
>   \<filter-mapping\>  
>   \<filter-name\>springSessionRepositoryFilter\</filter-name\>  
>   \<url-pattern\>/\*\</url-pattern\>  
>   \</filter-mapping\>

#### 在web.xml文件中加载Spring配置文件

>   \<context-param\>  
>   \<param-name\>contextConfigLocation\</param-name\>  
>   \<param-value\>classpath:applicationContext.xml\</param-value\>  
>   \</context-param\>  
>   \<listener\>  
>   \<listener-class\>org.springframework.web.context.ContextLoaderListener\</listener-class\>  
>   \</listener\>

#### 创建applicationContext-session.xml

##### 配置一个RedisHttpSessionConfiguration类

>   \<context:annotation-config/\>：用于激活已经在Spring容器中注册的bean或者注解，因为我们通过容器创建的bean中，底层有可能使用了其它的注解，我们通过\<context:component-scan\>就不能指定具体的包了，所以可以使用\<context:annotation-config/\>激活

>   \<!-- spring注解、bean的处理器 --\>  
>   \<context:annotation-config/\>  
>     
>   \<!-- Spring session 的配置类 --\>  
>   \<bean
>   class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration"/\>

##### 配置Spring-data-redis

>   \<!-- 配置jedis连接工厂，用于连接redis --\>  
>   \<bean id="jedisConnectionFactory"
>   class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory"\>  
>   \<property name="hostName" value="\${redis.hostName}"/\>  
>   \<property name="port" value="\${redis.port}"/\>  
>   \<property name="password" value="\${redis.password}"/\>  
>   \<property name="usePool" value="\${redis.usePool}"/\>  
>   \<property name="timeout" value="\${redis.timeout}"/\>  
>   \</bean\>  
>     
>   \<!--读取redis.properties属性配置文件--\>  
>   \<context:property-placeholder location="classpath:redis.properties"/\>

#### 配置redis.properties文件

>   redis.hostName=192.168.235.128  
>   redis.port=6379  
>   redis.password=123456  
>   redis.usePool=true  
>   redis.timeout=15000

#### 在applicationContext.xml中导入applicationContext-session.xml

![](media/dc5e1690414e2b8ad975aa6c3f22f33c.png)

>   点击config将这两个配置文件进行关联

![](media/97bd2e3f94b22f0c05f0bca20f542f1c.png)

![](media/ce09a6268f43f06388dd0ed18343b1e7.png)

### 部署测试

#### 思路

为了演示session的共享，我们这里配置两个tomcat服务器，端口号分别为9100和9200，将我们上面创建好的项目分别部署到这两台服务器上。一台服务器执行放session，另一台服务器执行取session的操作

#### 启动Linux上的redis服务器

#### 启动两台tomcat服务器

-   在浏览器中访问tomcat9100服务器的setSession

    ![](media/99c9a9c20a64fb8e761363083388c9cb.png)

-   在浏览器中访问tomcat9200服务器的getSession

    ![](media/ea01b24ebc2585b4065945f8aab07215.png)

#### 分析

>   tomcat9200服务器上的项目可以访问tomcat9100上的session，说明session共享成功

#### 进一步验证

打开Resis客户端工具（RedisDesktopManager），查看Redis里面的session数据

其实标准的redis的key格式就是用冒号分割，客户端工具会以目录的形式展示

![](media/06077447163709de1533e65e81723142.png)

# Spring Session常见的应用场景

## 同域名下相同项目（集群环境）实现Session共享

在同一个域名下，比如：[www.p2p.com](http://www.p2p.com)

同一个项目，部署了多台tomcat，这就是典型的集群。我们的入门案例就属于这种应用场景，只不过在实际开发的过程中，我们如果存在了tomcat集群，那么肯定会使用nginx进行负载均衡，那么这种情况下我们该如何处理。

### 案例设计思路

我们将上一个阶段的p2p项目实现集群部署下的Session共享，因为我们只是演示Session共享，所以我们试用一个简易版本的p2p，在我给大家提供的资料中，该p2p中只包含p2p和dataservice，在Linux服务器上，我们准备三台tomcat，其中两台部署p2p，并实现session共享，另一台部署dataservice

### 架构图

![](media/1d333b122a8079b5680df651f348f0d7.png)

### 实现步骤

#### 使用Xftp将p2p上传到tomcat9100和9200的webapps目录下

![](media/ed32b2f3cb08a540297395eaad525e17.png)

![](media/b35c40da34a8291b7b4e3b4e20a21934.png)

#### 使用Xftp将dataservice上传到tomcat9300的webapps目录下

![](media/4c0ae45b708fc5f000e2bdb410d221a7.png)

#### 使用资源下的SQL脚本，重新创建数据库的表

>   因为目前这个p2p的项目表结构和上一个阶段的稍微有些区别，所以我们这里更新一下

##### 启动mysql数据库

![](media/89a9a7cc6d48efb345dd67404a515c4f.png)

##### 通过MySQL客户端工具Navivat创建新的库

![](media/0abf01ba174d6283d70308c91456efb4.png)

##### 指定数据库名字为p2p2，字符集编码为utf-8

![](media/b869b3105c948fa572370c2340adcdc9.png)

##### 新建查询，执行p2p-data.sql脚本

![](media/624d320acc45a05acc396a5813d766c0.png)

##### 执行成功后，表结构如下

![](media/b4344f8c005cc63965e36962c6f17d4d.png)

#### 通过Xftp工具连接Linux，修改tomcat9300下的dataservice的连接信息

##### 使用记事本打开，修改redis.properties，保存

![](media/80a1c4c540e4209cfeddf7aafe17c5d9.png)

##### 修改datasource.properties，保存

![](media/b33cbdd3621cf02b2fe5fac11d7f6f57.png)

##### 修改applicationContext-dubbo-provide.xml注册中心的地址，并保存

![](media/64c371ee274c24e3c9f8a389df30ac3b.png)

#### 通过Xftp工具连接Linux，修改tomcat9100下的p2p的连接信息

>   这里只需要修改applicationContext-dubbo-consumer.xml文件中zk注册中心的地址即可

![](media/42f6d9c07585127d866bdf7d0b9a6dfb.png)

#### 通过Xftp工具连接Linux，修改tomcat9200下的p2p的连接信息

>   这里只需要修改applicationContext-dubbo-consumer.xml文件中zk注册中心的地址即可

![](media/75fb5d39bbb2a01cca47b37fc1297f50.png)

#### 确保Linux系统上的各应用服务器启动

>   注意：先通过ps –ef \| grep XXX命令查看，如果已经启动，就不需要再启动了

##### 启动ZooKeeper服务器

![](media/484e38ebe20dcb184cffc6fb23998a9d.png)

##### 启动MySQL服务器

![](media/45ab7fab50725d8ed09e58e7170e6cb3.png)

##### 启动Redis服务器

![](media/a2734c54af5011f796e55769b2a46864.png)

##### 启动tomcat9300服务器（为了避免出错先关闭，再启动）

![](media/ca0706f021989722eb783d7080f74b3c.png)

##### 启动tomcat9100服务器（为了避免出错先关闭，再启动）

![](media/f5dc8db6d133dfd232c80d1911c039d0.png)

##### 启动tomcat9200服务器（为了避免出错先关闭，再启动）

![](media/de7d0593fbac982cc4aa375ec3950a53.png)

##### 直接访问tomcat的方式，在浏览器输入地址访问tomcat9100和tomcat9200

![](media/7d237a541cab562a86e4483c4459dfc8.png)

![](media/e57cffbc2377f1ed9ac4d903962978e4.png)

#### 使用Nginx对tomcat9100和tomcat9200进行负载均衡

##### 负载均衡的配置，这里使用的是轮询策略

**upstream www.p2p.com{**

**server 127.0.0.1:9100;**

**server 127.0.0.1:9200;**

>   **}**

![](media/a8ad7dc2a7995855647427718b880de4.png)

##### location匹配的配置，注意：这里对静态资源的处理，我们暂时先注释掉

**location /p2p{**

**proxy_pass http://www.p2p.com;**

**}**

**如果要是实现了静态代理，别忘了启动所有的nginx服务器（负载\|代理）**

![](media/5e233420d874462faccad89b5fe0e298.png)

##### 重启Nginx

![](media/95d93b44f6d16fd4a40b2da0eb266ca9.png)

##### 在浏览器中输入地址，直接访问Nginx服务器，实现负载均衡

![](media/bd5b15c5680f2735ab1fe86c8b859102.png)

#### Nginx对集群负载均衡之后，登录不成功，但是直接访问tomcat9100或者tomcat9200都是可以成功登录的（Session丢失）

>   账号：13700000000 密码：123456

>   **分析原因**

>   因为默认我们负载均衡使用的是轮询策略，每次发送请求给nginx服务器，都会切换tomcat服务器，这个时候没有使用任何session共享策略，所以登录不成功

#### Nginx对集群负载均衡之后，Session共享方案

##### 修改nginx.conf配置文件，将轮询策略修改为ip_hash

![](media/e5a519458306781250e6de41ba4106ad.png)

>   但是这种情况，一旦ip发生变化，或者某台服务器出现故障，会重新分配，不稳定

>   所以我们看下这种情况后，将ip_hash注释掉

![](media/75d0effc08e43b59a06384a81a8974ca.png)

##### 使用SpringSession

使用Spring
Session实现session共享，我们不需要修改代码，只要修改一些配置文件即可，为了演示方便，我们直接使用Xftp修改已经发布到tomcat上的项目

-   向tomcat9100和tomcat9200的p2p项目中加jar包，这个jar包我已经准备好了

    ![](media/8ee443e802902eebe9927ac626de2134.png)

    ![](media/020657589f2cb6e60d1c6267d30678c5.png)

-   修改tomcat9100和tomcat9200的p2p项目的web.xml配置文件，添加Spring
    Session过滤器，因为我们项目本身已经通过springMVC启动了容器，所以spring监听器不需要加了，直接从01-springsession-web中拷贝即可

    ![](media/a02e81c11f09578bcd4e6123741452e7.png)

    ![](media/d3c000a9d66503df5f916e2aae5edbb4.png)

-   将01-springsession-web项目中resources下的applicationContext-session.xml和redis.properties拷贝到tomcat9100和tomcat9200的p2p项目WEB-INF/classes下

    ![](media/61250bc93cdd6b3c49d0aebf8ce4845d.png)

    ![](media/42c8372ff0974d3240888f24bbcb187b.png)

-   修改tomcat9100和tomcat9200的p2p项目WEB-INF/classes下的applicationContext.xml文件，引入applicationContext-session.xml

    ![](media/0cf0af7e94b55c503d0035913b48469b.png)

    ![](media/b79bb802a04c92e59cb2095b487de18e.png)

-   重启三台tomcat服务器，浏览器访问进行登录测试，可以实现Session共享

## 同域名下不同项目实现Session共享

在同一个域名下，有多个不同的项目（项目的上下文根不一样）比如：

www.web.com/p2p

www.web.com/shop

如图：

![](media/0023dcf13a699283f7c0eb3e884eb00b.png)

### 做法：设置Cookie路径为根/上下文

### 案例设计思路

在01-springsession-web项目的基础上，将本地tomcat9100的上下文根修改为p2p，将本地tomcat9200的上下文根修改为shop

### 实现步骤

#### 打开Edit Configurations进行配置

![](media/81d68c5dd6b679af0d65b7cc9f788a19.png)

#### 在Deployment选项卡下，设置本地tomcat9100的Application context为/p2p

![](media/ea770fa7e9a2d918a4476b489ce94f92.png)

#### 在Deployment选项卡下，设置本地tomcat9200的Application context为/shop

![](media/6dd6456803a8dfda5cb0dad5910c76a2.png)

#### 在idea中重新启动本地的两台tomcat服务器

#### 在浏览器中访问tomcat9100（p2p），设置session

![](media/182792c3902b29adce0a522975fa6fa5.png)

#### 在浏览器中访问tomcat9200（shop），获取session

![](media/4f95f2db3a7787e0e78ce40052f9d359.png)

#### 分析Session共享失败原因

我们通过浏览器提供的开发人员工具可以发现，这两个请求的cookie的路径(path)不一致，虽然我们已经加了Spring
Session共享机制，但是后台服务器认为这是两个不同的会话(session)，可以通过Redis客户端工具（Redis
Destop
Mananger）查看，先清空，然后访问，发现是维护了两个不同的session，所以不能实现共享

![](media/48dcd44c409c88b086f632cd53f4f589.png)

#### 解决方案 设置Cookie路径为根/上下文

>   在applicationContext-session.xml文件中，加如下配置：

>   \<!-- Spring session 的配置类 --\>  
>   \<bean
>   class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration"\>  
>   \<!--设置cookie的存放方式--\>  
>   \<property name="cookieSerializer" ref="defaultCookieSerializer"/\>  
>   \</bean\>  
>   \<!--设置cookie的存放方式具体实现--\>  
>   \<bean id="defaultCookieSerializer"
>   class="org.springframework.session.web.http.DefaultCookieSerializer"\>  
>   \<property name="cookiePath" value="/"/\>  
>   \</bean\>

#### 在idea中重新启动本地的两台tomcat服务器

#### 在浏览器中访问tomcat9100（p2p），设置session

![](media/182792c3902b29adce0a522975fa6fa5.png)

#### 在浏览器中访问tomcat9200（shop），获取session

![](media/5fae64d7bc959c721955370fdfe28c3b.png)

>   **注意：测试的时候要先清空浏览器缓存**

## 同根域名不同二级子域名下的项目实现Session共享

同一个根域名，不同的二级子域名

比如：

www.web.com

beijing.web.com

nanjing.web.com

如图：

![](media/deee583b0484847f3ce72ecd1571024a.png)

### 做法

-   设置Cookie路径为根/上下文，项目名一样的话，此步骤可以省略

-   设置cookie的域名为根域名 web.com

### 案例设计思路

在01-springsession-web项目的基础上，将本地tomcat9100的上下文根修改为p2p，将本地tomcat9200的上下文根修改为shop；在本机host文件中修改127.0.0.1的映射关系模拟不同的域名访问

### 实现步骤

#### 延续上面的案例的配置，两台本地tomcat服务器9100和9200，上下文根分别是p2p和shop

#### 修改本地hosts文件，加入如下配置

![](media/6275bd9282d2b9b939d46107fba336d7.png)

![](media/cbf16f5e0ead8ff8b64d9ca704d8cf0f.png)

#### 在idea中重新启动本地的两台tomcat服务器

#### 在浏览器中访问tomcat9100（p2p），设置session

>   **注意，这里不再使用localhost访问，而是使用我们映射的域名**

![](media/77a85758da6f9499d347c76ceca4d5a1.png)

#### 在浏览器中访问tomcat9200（shop），获取session

>   **注意，这里也不再使用localhost访问，而是使用我们映射的域名**

![](media/fd0b820dadc85df0f45fd73d572bcb82.png)

#### 分析Session共享失败原因

>   我们通过浏览器提供的开发人员工具可以发现，虽然这两个cookie的路径(path)都设置为了“/”，但是这两个cookie的域名不一致，虽然我们已经加了Spring
>   Session共享机制，但是后台服务器同样认为这是两个不同的会话(session)，可以通过Redis客户端工具（Redis
>   Destop
>   Mananger）查看，先清空，然后访问，发现是维护了两个不同的session，所以不能实现共享，也就是说后台区分是否同一个session和路径和域名有关。

![](media/23d7fb88e1a357210c2fe4724b69e7b2.png)

![](media/6066d2f89a5214035ba214aba5be0659.png)

#### 解决方案 设置Cook ie的域名为根域名 web.com

>   在applicationContext-session.xml文件中，加如下配置：

>   **注意:域名要和hosts文件中配置的域名后面一样**

>   \<!--设置cookie的存放方式具体实现--\>  
>   \<bean id="defaultCookieSerializer"
>   class="org.springframework.session.web.http.DefaultCookieSerializer"\>  
>   \<property name="cookiePath" value="/"/\>  
>   \<property name="domainName" value="web.com"/\>  
>   \</bean\>

![](media/32de928c7b41648184edf3f1ef6a01ac.png)

#### 在idea中重新启动本地的两台tomcat服务器

#### 在浏览器中访问tomcat9100（p2p），设置session

![](media/04100e1f72a0ac4b3fd123f12dfca282.png)

#### 在浏览器中访问tomcat9200（shop），获取session

![](media/3fa5950b7520b3035bcaaaa527ded766.png)

>   **注意：清空浏览器缓存**

## 单点登录（了解）

不同根域名下的项目实现Session共享，

比如阿里巴巴这样的公司，有多个业务线，多个网站，用户在一个网站登录，那么其他网站也会是登录了的状态，比如：登录了淘宝网，则天猫网也是登录的；

www.taobao.com

www.tmall.com

比如：

www.web.com

www.p2p.com

www.dai.com

对于不同根域名的场景，要实现一处登录，处处登录，**Spring Session不支持**

单点登录(Single Sign On)，简称为
SSO，是流行的企业业务整合的解决方案之一，SSO是指在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统

# Spring Session的执行流程（源码分析）

-   页面请求被全局的过滤器org.springframework.web.filter.DelegatingFilterProxy过滤

-   全局的过滤器是一个代理过滤器，它不执行真正的过滤逻辑，它代理了一个Spring容器中的名为：springSessionRepositoryFilter
    的一个过滤器

-   代理的这个 springSessionRepositoryFilter
    过滤器是从spring容器中获取的，真正执行过滤逻辑的是 SessionRepositoryFilter

    \@Bean注解

    相当于:

    \<bean id="springSessionRepositoryFilter"
    class="xx.xxx.xx.SessionRepositoryFilter"\>

    ........

    \</bean\>

-   该SessionRepositoryFilter过滤器覆盖了原来servlet中的request和response接口中定义的操作session方法，替换成自己的session方法

-   在过滤的时候，总是会执行一个finally语句块，在finally中提交session，保存到Redis

session以hash结构存放在redis

-   默认的过期时间30分钟

    \<!-- Spring session 的配置类 --\>  
    \<bean
    class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration"\>  
    \<!--设置session过期时间，单位是秒，默认是30分钟--\>  
    \<property name="maxInactiveIntervalInSeconds" value="3600"/\>  
    \<!--设置cookie的存放方式--\>  
    \<property name="cookieSerializer" ref="defaultCookieSerializer"/\>  
    \</bean\>
