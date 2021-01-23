常见web服务器软件 

1). webLogic：oracle公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。

 2). webSphere：IBM公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。 

3). JBOSS：JBOSS公司的，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。 

4). Tomcat：Apache基金组织，中小型的JavaEE服务器，仅仅支持少量的JavaEE规范 servlet/jsp。开源的，免费的。

# 一：简介

常见的web服务器

webLogic：oracle公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。 

webSphere：IBM公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。

JBOSS：JBOSS公司的，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。 

Tomcat：Apache基金组织，中小型的JavaEE服务器，仅仅支持少量的JavaEE规范 servlet/jsp。开源的，免费的。

历史







# 二：下载与安装

官网：

解压：

目录结构：



## 启动停止

启动：

双击 bin/startup.bat 文件 ；

停止：

双击 bin/shutdown.bat 文件 ；





# 三：Tomcat架构

## Http服务器请求处理

HTTP协议是浏览器与服务器之间的数据传送协议。作为应用层协议，HTTP是基于TCP/IP 协议来传递数据的（HTML文件、图片、查询结果等），HTTP协议不涉及数据包 （Packet）传输，主要规定了客户端和服务器之间的通信格式。

浏览器发给服务端的是一个HTTP格式的请求，HTTP服务器收到这个请求后，需要调用服 务端程序来处理，所谓的服务端程序就是你写的Java类，一般来说不同的请求需要由不同 的Java类来处理。



## Servlet工作流程

为了解耦，HTTP服务器不直接调用Servlet，而是把请求交给Servlet容器来处理

当客户请求某个资源时，HTTP服务器会用一个ServletRequest对象把客户的请求信息封 装起来，然后调用Servlet容器的service方法，Servlet容器拿到请求后，根据请求的URL 和Servlet的映射关系，找到相应的Servlet，如果Servlet还没有被加载，就用反射机制创 建这个Servlet，并调用Servlet的init方法来完成初始化，接着调用Servlet的service方法 来处理请求，把ServletResponse对象返回给HTTP服务器，HTTP服务器会把响应发送给 客户端。



Tomcat要实现两个 核心功能：

 1） 处理Socket连接，负责网络字节流与Request和Response对象的转化。

 2） 加载和管理Servlet，以及具体处理Request请求。

因此Tomcat设计了两个核心组件连接器（Connector）和容器（Container）来分别做这 两件事情。连接器负责对外交流，容器负责内部处理。

## 连接器Coyote

Coyote 是Tomcat的连接器框架的名称 , 是Tomcat服务器提供的供客户端访问的外部接 口。客户端通过Coyote与服务器建立连接、发送请求并接受响应 。

Coyote 封装了底层的网络通信（Socket 请求及响应处理），为Catalina 容器提供了统一 的接口，使Catalina 容器与具体的请求协议及IO操作方式完全解耦。Coyote 将Socket 输 入转换封装为 Request 对象，交由Catalina 容器进行处理，处理请求完成后, Catalina 通 过Coyote 提供的Response 对象将结果写入输出流 。

Coyote 作为独立的模块，只负责具体协议和IO的相关操作， 与Servlet 规范实现没有直 接关系，因此即便是 Request 和 Response 对象也并未实现Servlet规范对应的接口， 而 是在Catalina 中将他们进一步封装为ServletRequest 和 ServletResponse 。

### IO模型与协议

Tomcat的支持的IO模型（自8.5/9.0 版本起，Tomcat 移除了 对 BIO 的支持）：

NIO：非阻塞IO

NIO2：异步IO，采用JDK7中NIO2实现

APR：采用Apache可移植运行库实现，是C/C++编写的本地库。如果选择该方 案，需要单独安装APR库。

协议：

HTTP/1.1 这是大部分Web应用采用的访问协议。 

AJP 用于和Web服务器集成（如Apache），以实现对静态资源的优化以及 集群部署，当前支持AJP/1.3。

 HTTP/2 HTTP 2.0大幅度的提升了Web性能。下一代HTTP协议 ， 自8.5以及9.0 版本之后支持

### 连接器组件

**EndPoint**

 Coyote 通信端点，即通信监听的接口，是具体Socket接收和发送处理 器，是对传输层的抽象，因此EndPoint用来实现TCP/IP协议的。

Tomcat 并没有EndPoint 接口，而是提供了一个抽象类AbstractEndpoint ， 里面定 义了两个内部类：Acceptor和SocketProcessor。

Acceptor用于监听Socket连接请求。 SocketProcessor用于处理接收到的Socket请求，它实现Runnable接口，在Run方法里 调用协议处理组件Processor进行处理。

为了提高处理能力，SocketProcessor被提交到 线程池来执行。而这个线程池叫作执行器（Executor)

**Processor**  

 Coyote 协议处理接口 ，如果说EndPoint是用来实现TCP/IP协议的，那么 Processor用来实现HTTP协议，Processor接收来自EndPoint的Socket，读取字节流解 析成Tomcat Request和Response对象，并通过Adapter将其提交到容器处理， Processor是对应用层协议的抽象。

 **ProtocolHandler** 

Coyote 协议接口， 通过Endpoint 和 Processor ， 实现针对具体协 议的处理能力。

Tomcat 按照协议和I/O 提供了6个实现类 ： AjpNioProtocol ， AjpAprProtocol， AjpNio2Protocol ， Http11NioProtocol ，Http11Nio2Protocol ， Http11AprProtocol。

我们在配置tomcat/conf/server.xml 时 ， 至少要指定具体的 ProtocolHandler , 当然也可以指定协议名称 ， 如 ： HTTP/1.1 ，如果安装了APR，那么 将使用Http11AprProtocol ， 否则使用 Http11NioProtocol 。

 **Adapter**

 由于协议不同，客户端发过来的请求信息也不尽相同，Tomcat定义了自己的Request类 来“存放”这些请求信息。ProtocolHandler接口负责解析请求并生成Tomcat Request类。 但是这个Request对象不是标准的ServletRequest，也就意味着，不能用Tomcat Request作为参数来调



## 容器Catalina

Tomcat是一个由一系列可配置的组件构成的Web容器，而Catalina是Tomcat的servlet容 器。

Tomcat 本质上就是一款 Servlet 容器， 因此Catalina 才是 Tomcat 的核心， 其他模块 都是为Catalina 提供支撑的。

Catalina负责管理Server，而Server表示着整个服务器。Server下面有多个 服务Service，每个服务都包含着多个连接器组件Connector（Coyote 实现）和一个容器 组件Container。在Tomcat 启动的时候， 会初始化一个Catalina的实例。

### container结构

Tomcat设计了4种容器，分别是Engine、Host、Context和Wrapper。这4种容器不是平 行关系，而是父子关系。， Tomcat通过一种分层的架构，使得Servlet容器具有很好的灵 活性。

![image-20210123214152448](media/image-20210123214152448.png)

Engine 

表示整个Catalina的Servlet引擎，用来管理多个虚拟站点，一个Service 最多只能有一个Engine，但是一个引擎可包含多个Host

Host 

代表一个虚拟主机，或者说一个站点，可以给Tomcat配置多个虚拟主 机地址，而一个虚拟主机下可包含多个Context 

Context 

表示一个Web应用程序， 一个Web应用可包含多个Wrapper

 Wrapper 

表示一个Servlet，Wrapper 作为容器中的最底层，不能包含子容器



## 启动流程

步骤 : 

1） 启动tomcat ， 需要调用 bin/startup.bat (在linux 目录下 , 需要调用 bin/startup.sh) ， 在startup.bat 脚本中, 调用了catalina.bat。 

2） 在catalina.bat 脚本文件中，调用了BootStrap 中的main方法。

 3）在BootStrap 的main 方法中调用了 init 方法 ， 来创建Catalina 及 初始化类加载器。

 4）在BootStrap 的main 方法中调用了 load 方法 ， 在其中又调用了Catalina的load方 法。 

5）在Catalina 的load 方法中 , 需要进行一些初始化的工作, 并需要构造Digester 对象, 用 于解析 XML。 

6） 然后在调用后续组件的初始化操作 。。。 加载Tomcat的配置文件，初始化容器组件 ，监听对应的端口号， 准备接受客户端请求



### 生命周期 Lifecycle 

由于所有的组件均存在初始化、启动、停止等生命周期方法，拥有生命周期管理的特性， 所以Tomcat在设计的时候， 基于生命周期管理抽象成了一个接口 Lifecycle ，而组 件 Server、Service、Container、Executor、Connector 组件 ， 都实现了一个生命周期 的接口，从而具有了以下生命周期中的核心方法： init（）：初始化组件 

start（）：启动组件 

stop（）：停止组件 

destroy（）：销毁组件



## 请求处理流程

设计了这么多层次的容器，Tomcat是怎么确定每一个请求应该由哪个Wrapper容器里的 Servlet来处理的呢？答案是，Tomcat是用Mapper组件来完成这个任务的。

Mapper组件的功能就是将用户请求的URL定位到一个Servlet，它的工作原理是： Mapper组件里保存了Web应用的配置信息，其实就是容器组件与访问路径的映射关系， 比如Host容器里配置的域名、Context容器里的Web应用路径，以及Wrapper容器里 Servlet映射的路径，你可以想象这些配置信息就是一个多层次的Map

步骤: 

1) Connector组件Endpoint中的Acceptor监听客户端套接字连接并接收Socket。

2) 将连接交给线程池Executor处理，开始执行请求响应任务。

3) Processor组件读取消息报文，解析请求行、请求体、请求头，封装成Request对象。 

4) Mapper组件根据请求行的URL值和请求头的Host值匹配由哪个Host容器、Context容 器、Wrapper容器处理请求。 

5) CoyoteAdaptor组件负责将Connector组件和Engine容器关联起来，把生成的 Request对象和响应对象Response传递到Engine容器中，调用 Pipeline。

6) Engine容器的管道开始处理，管道中包含若干个Valve、每个Valve负责部分处理逻 辑。执行完Valve后会执行基础的 Valve--StandardEngineValve，负责调用Host容器的 Pipeline。 

7) Host容器的管道开始处理，流程类似，最后执行 Context容器的Pipeline。 

8) Context容器的管道开始处理，流程类似，最后执行 Wrapper容器的Pipeline。

9) Wrapper容器的管道开始处理，流程类似，最后执行 Wrapper容器对应的Servlet对象 的 处理方法。



# 服务器配置

Tomcat 服务器的配置主要集中于 tomcat/conf 下的 catalina.policy、 catalina.properties、context.xml、server.xml、tomcat-users.xml、web.xml 文件。

## server.xml

server.xml 是tomcat 服务器的核心配置文件，包含了Tomcat的 Servlet 容器 （Catalina）的所有配置。

### server

Server是server.xml的根元素，用于创建一个Server实例，默认使用的实现类是 org.apache.catalina.core.StandardServer。

```xml
<Server port="8005" shutdown="SHUTDOWN">
...
</Server>
```

port : Tomcat 监听的关闭服务器的端口。 

shutdown： 关闭服务器的指令字符串。

Server内嵌的子元素为 Listener、GlobalNamingResources、Service。

###  Service 

该元素用于创建 Service 实例，默认使用 org.apache.catalina.core.StandardService。 默认情况下，Tomcat 仅指定了Service 的名称， 值为 "Catalina"。Service 可以内嵌的 元素为 ： Listener、Executor、Connector、Engine，

其中 ：

 Listener 用于为Service 添加生命周期监听器，

 Executor 用于配置Service 共享线程池，

Connector 用于配置 Service 包含的链接器，

 Engine 用于配置Service中链接器对应的Servlet 容器引擎。

 一个Server服务器，可以包含多个Service服务。

###  Executor

 默认情况下，Service 并未添加共享线程池配置。 如果我们想添加一个线程池， 可以在 下添加如下配置：

### Connector 

Connector 用于创建链接器实例。默认情况下，server.xml 配置了两个链接器，一个支 持HTTP协议，一个支持AJP协议。因此大多数情况下，我们并不需要新增链接器配置， 只是根据需要对已有链接器进行优化。

### Engine 

作为Servlet 引擎的顶级元素，内部可以嵌入： Cluster、Listener、Realm、 Valve和Host。

### Host

 Host 元素用于配置一个虚拟主机， 它支持以下嵌入元素：Alias、Cluster、Listener、 Valve、Realm、Context。如果在Engine下配置Realm， 那么此配置将在当前Engine下 的所有Host中共享。 同样，如果在Host中配置Realm ， 则在当前Host下的所有Context 中共享。Context中的Realm优先级 > Host 的Realm优先级 > Engine中的Realm优先 级。

##  tomcat-users.xml

 该配置文件中，主要配置的是Tomcat的用户，角色等信息，用来控制Tomcat中 manager， host-manager的访问权限。

## Web.xml

web.xml 是web应用的描述文件， 它支持的元素及属性来自于Servlet 规范定义 。 在 Tomcat 中， Web 应用的描述信息包括 tomcat/conf/web.xml 中默认配置 以及 Web 应用 WEB-INF/web.xml 下的定制配置。

### 会话配置 

用于配置Web应用会话，包括 超时时间、Cookie配置以及会话追踪模式。它将覆盖 server.xml 和 context.xml 中的配置。

```xml
<session‐config>
<session‐timeout>30</session‐timeout>
<cookie‐config>
<name>JESSIONID</name>
<domain>www.xqc.cn</domain>
<path>/</path>
<comment>Session Cookie</comment>
<http‐only>true</http‐only>
<secure>false</secure>
<max‐age>3600</max‐age>
</cookie‐config>
<tracking‐mode>COOKIE</tracking‐mode>
</session‐config>
```



### servlet配置

Servlet 的配置主要是两部分， servlet 和 servlet-mapping ：

### Listener配置 

Listener用于监听servlet中的事件，例如context、request、session对象的创建、修 改、删除，并触发响应事件。Listener是观察者模式的实现，在servlet中主要用于对 context、request、session对象的生命周期进行监控。在servlet2.5规范中共定义了8中 Listener。在启动时，ServletContextListener 的执行顺序与web.xml 中的配置顺序一 致， 停止时执行顺序相反。

### Filter配置

filter 用于配置web应用过滤器， 用来过滤资源请求及响应。 经常用于认证、日志、加 密、数据转换等操作，