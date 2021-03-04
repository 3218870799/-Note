**之所以有 Spring CloudAlibaba,是因为 Spring Cloud Netflix 项目进入维护模式，就是不更新了,不会开发新组件了**

**所以,某些组件都有代替版了,比如 Ribbon 由 Loadbalancer 代替,等等**

==支持的功能==

服务限流降级：默认支持 Serlvet，Feign，RestTemplate，Dubbo 和 RocketMQ 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。

服务注册与发现：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。

分布式配置管理：致辞分布式系统中的外部化配置，配置更改时自动刷新。

消息驱动能力：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力

阿里云对象存储：阿里云提供的海量，安全，低成本，高可用的云存储服务，支持在任何应用，任何时间，任何地点存储和访问任意类型的数据。

分布式任务调度：提供秒级，精准，高可靠，高可用的定时（基于 Cron 表达式）任务调度服务，同时提供分布式的任务执行模型，如网格任务，网格任务支持海量子任务均匀分配到所有 Worker(schedulerx-client)上执行。

几乎可以将之前的 Spring Cloud 代替

具体组件

Sentinal：阿里巴巴开源产品，把流量作为切入点，从流量控制，熔断降级，系统负载保护等多个维度保护服务的稳定性。

Nacos：一个更易于构建云原生应用的动态服务发现，配置管理和服务管理平台。

RocketMQ：基于 Java 的高性能，高吞吐量的分布式消息和流计算平台。

Dubbo：高性能 Java RPC 框架。

Seata：一个易于使用的高性能微服务分布式事务解决方案。

Alibaba Cloud OSS：阿里云存储服务，是阿里云提供的海量，安全，低成本，高可靠的云存储服务，您可以在任何应用，任何时间，任何地点存储和访问任意类型的数据。

# Nacos

## 对比

Nacos 它既可以支持 CP,也可以支持 AP,可以切换

![](media/Alibaba%E7%9A%8412.png)

何时选择使用何种模式？既然支持 CP 和 AP，那么如何选择？

一般来说，如果不需要存储服务级别的信息且服务实例是通过 Nacos-Client 注册，并能够保持心跳上报，那么就可以选择 AP 模式，当前主流的服务如 SpringCloud 和 Dubbo 服务，都适用 AP 模式，AP 模式为了服务的可能性减弱了一致性，因此 AP 模式下只能注册临时实例。

如果需要在服务级别编辑或则存储配置信息，那么 CP 是必须，K8S 服务和 DNS 服务则适用于 CP 模式。CP 模式下则之策注册持久化实例，此时则是以 raft 协议为集群运行模式。该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误。

==下面这个 curl 命令,就是切换模式==

`curl -X PUT ‘$NACOS_SERVER:8848/nacos/v1/operator/switches?entry=serverMode&value=CP’`

**服务注册和配置中心的组合**

Nacos=erueka+config+bus

## 安装

Window 安装：

需要 java8 和 Maven

**1,到 github 上下载安装包**

https://github.com/alibaba/nacos/releases/tag/2.0.0-BETA

解压安装包

导入数据库脚本：D:\Nacos\conf\nacos_config.sql

启动 mysql

**2,启动 Nacos**

在 bin 下,进入 cmd，如果集群直接启动

./startup.cmd

单机使用单机模式启动

```cmd
.\startup.cmd -m standalone
```

**3,访问 Nacos**

Nacos 默认监听 8848

http://localhost:8848/nacos

账号密码:默认都是 nacos

## 原理

流程：

心跳机制：启动微服务时会向 Nacos 建立连接，并发送心跳请求，Nacos 会将其记录下来，如果某个微服务挂掉了，Nacos 定时任务监听微服务是否超出心跳时间，先标记为不健康，还是不行就直接干掉

### 使用 Nacos:

新建 pay 模块

**现在不需要额外的服务注册模块了,Nacos 单独启动了**

名字: cloudalibaba-pay-9001

1,pom

父项目管理 alibaba 的依赖:

![](media/Alibaba%E7%9A%844.png)

![](media/Alibaba%E7%9A%843.png)

==9001 的 pom==:

另外一个文件.....

2,配置文件

![](media/Alibaba%E7%9A%845.png)

3,启动类

![](media/Alibaba%E7%9A%846.png)

4,controller:

![](media/Alibaba%E7%9A%847.png)

5,测试

启动 9001

然后查看 Nacos 的 web 界面,可以看到 9001 已经注册成功

###

### 创建其他 Pay 模块

额外在创建 9002,9003

直接复制上面的即可

### 创建 order 模块

名字: cloudalibaba-order-83

1,pom

**为什么 Nacos 支持负载均衡?**

Nacos 直接集成了 Ribon,所以有负载均衡

2,配置文件

![](media/Alibaba%E7%9A%848.png)

**这个 server-url 的作用是,我们在 controller,需要使用 RestTempalte 远程调用 9001,**

**这里是指定 9001 的地址**

3,主启动类

![](media/Alibaba%E7%9A%849.png)

4,编写配置类

==因为 Naocs 要使用 Ribbon 进行负载均衡,那么就需要使用 RestTemplate==

![](media/Alibaba%E7%9A%8410.png)

5,controller:

![](media/Alibaba%E7%9A%8411.png)

6,测试

启动 83,访问 9001,9002,可以看到,实现了负载均衡

### 使用 Nacos 作为配置中心:

Nacos 同 SpringCloud-Config 一样，在项目初始化时，要保证先从配置中心进行配置拉取，拉取配置后，才能保证项目的正常启动。

SpringBoot 中配置文件的加载时存在优先级顺序的，Bootstrap 优先级高于 application

**==需要创建配置中心的客户端模块==**

cloudalibaba-Nacos-config-client-3377

1,pom

2,配置文件

这里需要配置两个配置文件,application.ymk 和 bootstarp.yml

主要是为了可以与 spring clodu config 无缝迁移

bootstrap.yml

```yml
# nacos配置
server:
	port: 3377
spring:
	application:
		name: nacos-config-client
	cloud:
		nacos:
			discovery:
				server-addr: localhost:8848 #Nacos服务注册中心地址
			config:    #指定作为配置中心在的Naocs的地址
				server-addr localhost:8848 #Nacos作为配置中心地址
				file-extension: yaml #指定yamL格式的配置

```

```java
可以看到
```

application.yml

```yml
spring:
	profiles: #表示要去配置中心拉取xxx-devxx.yml的配置文件
		active:dev # 表示开发环境
```

3,主启动类

![](media/Alibaba%E7%9A%8418.png)

4,controller

![](media/Alibaba%E7%9A%8417.png)

可以看到,这里也添加了@RefreshScope 之前在 Config 配置中心,也是添加这个注解实现动态刷新的

通过 SpringCloud 原生注解 @RefreshScope 实现配置自动更新

5,在 Nacos 添加配置信息:

==**Nacos 的配置规则:**==

Nacos 中的 dataId 的组成格式及与 SpringBoot 配置文件中的匹配规则

**配置规则,就是我们在客户端如何指定读取配置文件,配置文件的命名的规则**

默认的命名方式:

![](media/Alibaba%E7%9A%8421.png)

```java
prefix:
		默认就是当前服务的服务名称
 		也可以通过spring.cloud.necos.config.prefix配置
spring.profile.active:
		就是我们在application.yml中指定的,当前是开发环境还是测试等环境
    这个可以不配置,如果不配置,那么前面的 -  也会没有
file-extension
     就是当前文件的格式(后缀),目前只支持yml和properties
```

![](media/Alibaba%E7%9A%8424.png)

==在 web UI 上创建配置文件:==

![](media/Alibaba%E7%9A%8422.png)

![](media/Alibaba%E7%9A%8423.png)

注意,DataId 就是配置文件名字:

名字一定要按照上面的==规则==命名,否则客户端会读取不到配置文件

6,测试

重启 3377 客户端

访问 3377

![](media/Alibaba%E7%9A%8426.png)

**拿到了配置文件中的值**

7,注意默认就开启了自动刷新

此时我们修改了配置文件

客户端是可以立即更新的

因为 Nacos 支持 Bus 总线,会自动发送命令更新所有客户端

### Nacos 配置中心之分类配置:

问题：实际开发中，通常一个系统会准备 dev 开发环境，test 测试环境，prod 生产环境，如果保证指定环境启动时服务能正确读取到 Nacos 上的响应配置文件呢？

问题：大型分布式微服务系统会有很多微服务子项目，每个微服务项目又都会有响应的开发环境，测试环境，预发环境，正式环境，那怎么对这些微服务配置进行管理呢？

![](media/Alibaba%E7%9A%8428.png)

Namespace + Group + Data ID 三者关系？为什么这么设计？

NameSpace 默认有一个:public 名称空间

这三个类似 java 的: 包名 + 类名 + 方法名

最外城的 namespace 是可以用去区分部署环境的，Group 和 DayaID 逻辑上区分两个目标对象

三者情况：

![image-20210105161634149](media/image-20210105161634149.png)

默认情况：

Namespace = public ，Group = DEFAULT_GROUP，默认 Cluster 是 Default

Nacos 默认命名空间是 public Namespace 主要用来实现隔离。

比方说我们现在有三个环境：开发，测试，生产环境，我们就可以创建三个 Namespace，不同的 Namespace 之间是隔离的。

Group 默认是 DEFAULT_GROUP,Group 可以把不同的微服务划分到同一个分组里面去。

Service 就是微服务：一个 Service 可以包含多个 Cluster（集群），Nacos 默认 Cluster 是 DEFAULT，Cluster 是对指定微服务的一个虚拟划分。

比方说为了容灾，将 Service 微服务分别部署在杭州机房和广州机房，这是就可以给杭州机房的 Service 微服务起一个集群名称（HZ），给广州机房的 Service 微服务起一个集群名称（GZ），还可以尽量让同一个机房的微服务相互调用，以提高性能。

#### 1,配置不同 DataId:

![](media/Alibaba%E7%9A%8432.png)

![](media/Alibaba%E7%9A%8433.png)

==通过配置文件,实现多环境的读取:==

![](media/Alibaba%E7%9A%8434.png)

```java
此时,改为dev,就会读取dev的配置文件,改为test,就会读取test的配置文件
```

#### 2,配置不同的 GroupID:

直接在新建配置文件时指定组

![](media/Alibaba%E7%9A%8435.png)

![](media/Alibaba%E7%9A%8436.png)

==在客户端配置,使用指定组的配置文件:==

Bootstrap + application

**这两个配置文件都要修改**

![](media/Alibaba%E7%9A%8438.png)

重启服务,即可

#### 配置不同的 namespace:

![](media/Alibaba%E7%9A%8439.png)

![](media/Alibaba%E7%9A%8442.png)

==客户端配置使用不同名称空间:==

![](media/Alibaba%E7%9A%8441.png)

**要通过命名空间 id 指定**

OK,测试

### Nacos 集群和持久化配置:

![](media/Alibaba%E7%9A%8445.png)

Nacos 默认有自带嵌入式数据库,derby,但是如果做集群模式的话,就不能使用自己的数据库，不然每个节点一个数据库,那么数据就不统一了,需要使用外部的 mysql

采用集中式存储的方式来支持集群化部署，目前只支持 MYSQL 的存储。

#### 1,单机版,切换 mysql 数据库:

**将 nacos 切换到使用我们自己的 mysql 数据库:**

**1,nacos 默认自带了一个 sql 文件,在 nacos 安装目录下**

将它放到我们的 mysql 执行

**2,修改 Nacos 安装目录下的安排 application.properties,添加:**

```properties
spring.datasource.platform=mysql
db.num=1
db.ur1.O=jdbc : mysql://127.0.0.1:3306/nacos_config?
characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=truedb.user=rodt
db. password=123456
```

**3,此时可以重启 nacos,那么就会改为使用我们自己的 mysql**

#### Linux 上配置 Nacos 集群+Mysql 数据库

官方架构图:

![](media/Alibaba%E7%9A%8445.png)

**需要一个 Nginx 作为 VIP**

1,下载安装 Nacos 的 Linux 版安装包

2,进入安装目录,现在执行自带的 sql 文件

进入 mysql,执行 sql 文件

3.修改配置文件,切换为我们的 mysql

就是上面 windos 版要修改的几个属性

4,修改 cluster.conf,指定哪几个节点是 Nacos 集群

这里使用 3333,4444,5555 作为三个 Nacos 节点监听的端口

5,我们这里就不配置在不同节点上了,就放在一个节点上

既然要在一个节点上启动不同 Nacos 实例,就要修改 startup.sh,使其根据不同端口启动不同 Nacos 实例

![](media/Alibaba%E7%9A%8448.png)

![](media/Alibaba%E7%9A%8449.png)

可以看到,这个脚本就是通过 jvm 启动 nacos

所以我们最后修改的就是,nohup java -Dserver.port=3344

6,配置 Nginx

```properties
upstream cluster{
	server 127.0.0.1:3333;
	server 127.0.0.1:4444;
	server 127.0.0.1:5555;
}
server [
	listen 1111:
	server_name localhost;
	# charset koi8- r:
	#access_log logs / host.access.logmain;
	location {
		#roothtml:
		#index  index.html index.htm;
		proxy_pass http: // cluster:
	}
```

7,启动 Nacos:
./startup.sh -p 3333

./startup.sh -p 4444

./startup.sh -p 5555

7,启动 nginx

8,测试:

访问 192.168.159.121:1111

如果可以进入 nacos 的 web 界面,就证明安装成功了

9,将微服务注册到 Nacos 集群:

```yml
spring:
	application:
		name: nacos-payment-provider
	cloud:
		nacos:
			discovery:
			#server-addr: localhost:8848#/配置Nacos地址
			#换成nginx的1111端口，l做集群
			server-addr: 192.168.111.144:1111
					# 换成nginx的即可,有nginx代理到其中一个节点
```

10,进入 Nacos 的 web 界面

可以看到,已经注册成功

![](media/Alibaba%E7%9A%8452.png)

# Sentinel

实现熔断与限流,就是 Hystrix

![](media/Alibaba%E7%9A%8453.png)

![](media/Alibaba%E7%9A%8454.png)

## Window 安装

下载：https://github.com/alibaba/Sentinel/releases是一个Jar包，放到目录下

![image-20210304102735678](media/image-20210304102735678.png)

创建一个启动脚本，指定端口为 8070

```cmd
start java -jar sentinel-dashboard-1.8.1.jar --server.port=8070
```

然后访问：http://localhost:8070/

账号：sentinel

密码：sentinel

## 微服务整合 sentinel:

1,启动 Nacos

2,新建一个项目,8401,主要用于配置 sentinel

1. pom

2. 配置文件

   ```yml
   server:
   	port: 8401
   spring:
   	application:
   		name: cloudalibaba-sentinel-service
   	cloud:
   		nacos:
   			discovery:
   			#Nacos服务注册中心地址
   				server-addr: localhost:8848
   		sentinel:
   			transport:
   				#配置sentinel dashboard地址
   				dashboard: localhost:8080
   				#默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
   				port: 8719I
   management:
   	endpoints:
   		web:
   			exposure:
   				include: '*'

   ```

3. 主启动类

   ![](media/Alibaba%E7%9A%8456.png)

4. controller\

   ![](media/sentinel%E7%9A%841.png)

5. 到这里就可以启动 8401

   此时我们到 sentinel 中查看,发现并 8401 的任何信息

   是因为,sentinel 是懒加载,需要我们执行一次访问,才会有信息

   访问 localhost/8401/testA

   ![](media/sentinel%E7%9A%842.png)

6. 可以看到.已经开始监听了

## sentinel 的流控规则

流量限制控制规则

![](media/sentinel%E7%9A%847.png)

![](media/sentinel%E7%9A%843.png)

资源名：唯一名称。默认请求路径

针对来源：Sentinel 可以针对调用者进行限流，填写微服务名，默认 default（不区分来源）

阈值类型/单机阈值：

- QPS（每秒钟请求数量）：当调用该 API 的 QPS 达到阈值的时候，进行限流。
- 线程数：当调用该 API 的线程数达到阈值的时候，进行限流。

是否集群：不需要集群

流控模式：

- 直接：API 达到限流条件时，直接限流
- 关联：当关联的资源达到阈值是，就限流自己
- 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）

流控效果

- 快速失败：直接失败，跑一场
- Warm UP：根据 CodeFactor（冷加载因子，默认 3）的值，从阈值 CodeFactor，经过预热时长，才达到设置的 QPS 的阈值。

==流控模式==:

1. 直接快速失败

   ![](media/sentinel%E7%9A%845.png)

   ==直接失败的效果:==

   ![](media/sentinel%E7%9A%846.png)

2. 线程数:

   线程数：当调用该 AP 的线程数达到阈值的时候，进行限流。

   ![](media/sentinel%E7%9A%8410.png)

   ```
   比如a请求过来,处理很慢,在一直处理,此时b请求又过来了
   		此时因为a占用一个线程,此时要处理b请求就只有额外开启一个线程
   		那么就会报错
   ```

   ![](media/sentinel%E7%9A%8411.png)

3. 关联:

   当关联的资源达到阈值时，就限流自己

   当与 A 关联的资源 B 达到阈值后，就限流 A 自己

   ![](media/sentinel%E7%9A%8412.png)

   ==应用场景: 比如**支付接口**达到阈值,就要限流下**订单的接口**,防止一直有订单==

   ![](media/sentinel%E7%9A%8413.png)

   **当 testA 达到阈值,qps 大于 1,就让 testB 之后的请求直接失败**

   可以使用 postman 压测

4. 链路:
   多个请求调用同一个微服务

5. 预热 Warm up:

   Warm UP(RuleConstant.CONTROL_BEHAVIOR_WARM_UP)方式，即预热冷启动方式，当系统长期处于低水位的情况下，当流量突然增加时，直接把系统拉升到高水位可能瞬间把系统压垮，通过冷启动，让通过的李陆良缓慢增加，在一定时间内逐渐增加到阈值上限，给冷系统一个预热的时间，避免冷系统被压垮

   Warm up：根据 codeFactor（冷加载因子，默认 3）的值，从阈值 codeFactor，经过预热时长，才达到设置的 QPS 阈值。

   ![](media/sentinel%E7%9A%8414.png)

   ![](media/sentinel%E7%9A%8415.png)

   ![](media/sentinel%E7%9A%8416.png)

   ==应用场景==

   ![](media/sentinel%E7%9A%8417.png)

6. 排队等待:

   ![](media/sentinel%E7%9A%8418.png)

   ![](media/sentinel%E7%9A%8419.png)

## 降级规则

**就是熔断降级**

![](media/sentinel%E7%9A%8421.png)

![](media/sentinel%E7%9A%8420.png)

![](media/sentinel%E7%9A%8422.png)

![](media/sentinel%E7%9A%8423.png)

#### 1,RT 配置:

新增一个请求方法用于测试

![](media/sentinel%E7%9A%8424.png)

==配置 RT:==

这里配置的 PT,默认是秒级的平均响应时间

![](media/sentinel%E7%9A%8425.png)

默认计算平均时间是: 1 秒类进入 5 个请求,并且响应的平均值超过阈值(这里的 200ms),就报错]

1 秒 5 请求是 Sentinel 默认设置的

==测试==

![](media/sentinel%E7%9A%8427.png)

![](media/sentinel%E7%9A%8426.png)

**默认熔断后.就直接抛出异常**

#### 2,异常比例:

![](media/sentinel%E7%9A%8428.png)

修改请求方法

![](media/sentinel%E7%9A%8429.png)

配置:

![](media/sentinel%E7%9A%8431.png)

==如果没触发熔断,这正常抛出异常==:

![](media/sentinel%E7%9A%8432.png)

==触发熔断==:

![](media/sentinel%E7%9A%8433.png)

#### 3, 异常数:

![](media/sentinel%E7%9A%8434.png)

![](media/sentinel%E7%9A%8435.png)

一分钟之内,有 5 个请求发送异常,进入熔断

## 热点规则

![](media/sentinel%E7%9A%8436.png)

![](media/sentinel%E7%9A%8437.png)

比如:

localhost:8080/aa?name=aa

localhost:8080/aa?name=b'b

加入两个请求中,带有参数 aa 的请求访问频次非常高,我们就现在 name==aa 的请求,但是 bb 的不限制

==如何自定义降级方法,而不是默认的抛出异常?==

![](media/sentinel%E7%9A%8438.png)

**使用@SentinelResource 直接实现降级方法,它等同 Hystrix 的@HystrixCommand**

![](media/sentinel%E7%9A%8439.png)

==定义热点规则:==

![](media/sentinel%E7%9A%8440.png)

![](media/sentinel%E7%9A%8442.png)

**此时我们访问/testHotkey 并且带上才是 p1**

如果 qps 大于 1,就会触发我们定义的降级方法

![](media/sentinel%E7%9A%8441.png)

**但是我们的参数是 P2,就没有问题**

![](media/sentinel%E7%9A%8444.png)

只有带了 p1,才可能会触发热点限流

![](media/sentinel%E7%9A%8443.png)

#### 2,设置热点规则中的其他选项:

![](media/sentinel%E7%9A%8445.png)

**需求:**

![](media/sentinel%E7%9A%8446.png)

![](media/sentinel%E7%9A%8447.png)

==测试==

![](media/sentinel%E7%9A%8448.png)

![](media/sentinel%E7%9A%8449.png)

**注意:**

参数类型只支持,8 种基本类型+String 类

==注意:==

如果我们程序出现异常,是不会走 blockHander 的降级方法的,因为这个方法只配置了热点规则,没有配置限流规则

我们这里配置的降级方法是 sentinel 针对热点规则配置的

只有触发热点规则才会降级

![](media/sentinel%E7%9A%8450.png)

### 3,系统规则:

系统自适应限流:
从整体维度对应用入口进行限流

对整体限流,比如设置 qps 到达 100,这里限流会限制整个系统不可以

_![](media/sentinel%E7%9A%8451.png)_

![](media/sentinel%E7%9A%8452.png)

==测试==:
![](media/sentinel%E7%9A%8453.png)

![](media/sentinel%E7%9A%8454.png)

### @SentinelResource 注解:

**用于配置降级等功能**

1,环境搭建

1. 为 8401 添加依赖

   添加我们自己的 commone 包的依赖

   ![](media/sentinel%E7%9A%8455.png)

2. 额外创建一个 controller 类

   ![](media/sentinel%E7%9A%8456.png)

3. 配置限流

   **注意,我们这里配置规则,资源名指定的是@SentinelResource 注解 value 的值,**

   **这样也是可以的,也就是不一定要指定访问路径**

   ![](media/sentinel%E7%9A%8457.png)

4. 测试.

   可以看到已经进入降级方法了

   ![](media/sentinel%E7%9A%8458.png)

5. ==此时我们关闭 8401 服务==

   可以看到,这些定义的规则是临时的,关闭服务,规则就没有了

   ![](media/sentinel%E7%9A%8459.png)

**可以看到,上面配置的降级方法,又出现 Hystrix 遇到的问题了**

降级方法与业务方法耦合

每个业务方法都需要对应一个降级方法

#### 自定义限流处理逻辑:

1. ==单独创建一个类,用于处理限流==

   ![](media/sentinel%E7%9A%84%E7%9A%841.png)

2. ==在 controller 中,指定使用自定义类中的方法作为降级方法==

   ![](media/sentinel%E7%9A%84%E7%9A%842.png)

3. ==Sentinel 中定义流控规则==:

   这里资源名,是以 url 指定,也可以使用@SentinelResource 注解 value 的值指定

   ![](media/sentinel%E7%9A%84%E7%9A%845.png)

4. ==测试==:

   ![](media/sentinel%E7%9A%84%E7%9A%843.png)

5. ==整体==:

   ![](media/sentinel%E7%9A%84%E7%9A%844.png)

6.

#### @SentinelResource 注解的其他属性:

![](media/sentinel%E7%9A%84%E7%9A%847.png)

![](media/sentinel%E7%9A%84%E7%9A%846.png)

### 服务熔断:

1. **启动 nacos 和 sentinel**

2. **新建两个 pay 模块 9003 和 9004**

   1. pom

   2. 配置文件

      ![](media/sentinel%E7%9A%84%E7%9A%848.png)\*

   3. 主启动类

      ```java
      @SpringBootApplication
      @EnableDiscoveryClient
      public class PaymentMain9003 {

          public static void main(String[] args) {
              SpringApplication.run(PaymentMain9003.class,args);
          }
      }
      ```

      ```

      ```

   4. controller

      ![](media/sentinel%E7%9A%84%E7%9A%849.png)

      **然后启动 9003.9004**

3. **新建一个 order-84 消费者模块:**

   1. pom

      与上面的 pay 一模一样

   2. 配置文件

      ![](media/sentinel%E7%9A%84%E7%9A%8410.png)

   3. 主启动类

      ![](media/sentinel%E7%9A%84%E7%9A%8411.png)

   4. 配置类

      ![](media/sentinel%E7%9A%84%E7%9A%8412.png)

   5. controller

      ![](media/sentinel%E7%9A%84%E7%9A%8413.png)

   6. **==为业务方法添加 fallback 来指定降级方法==**:

      ![](media/sentinel%E7%9A%84%E7%9A%8414.png)

      ==重启 order==

      测试:

      ![](media/sentinel%E7%9A%84%E7%9A%8415.png)

      ==所以,fallback 是用于管理异常的,当业务方法发生异常,可以降级到指定方法==

      注意,我们这里==并没有使用 sentinel 配置任何规则==,但是却降级成功,就是因为

      fallback 是用于管理异常的,当业务方法发生异常,可以降级到指定方法==

   7. **==为业务方法添加 blockHandler,看看是什么效果==**

      ![](media/sentinel%E7%9A%84%E7%9A%8416.png)

      **重启 84,访问业务方法:**

      ![](media/sentinel%E7%9A%84%E7%9A%8417.png)

      可以看到.,直接报错了,并没有降级

      也就是说,blockHandler==只对 sentienl 定义的规则降级==

   8. **==如果 fallback 和 blockHandler 都配置呢?==**]

      ![](media/sentinel%E7%9A%84%E7%9A%8418.png)

      **设置 qps 规则,阈值 1**

      ![](media/sentinel%E7%9A%84%E7%9A%8419.png)

      ==测试:==

      ![](media/sentinel%E7%9A%84%E7%9A%8420.png)

      可以看到,当两个都同时生效时,==blockhandler 优先生效==

   9. **==@SentinelResource 还有一个属性,exceptionsToIgnore==**

      ![](media/sentinel%E7%9A%84%E7%9A%8421.png)

      **exceptionsToIgnore 指定一个异常类,**

      **表示如果当前方法抛出的是指定的异常,不降级,直接对用户抛出异常**

      ![](media/sentinel%E7%9A%84%E7%9A%8422.png)

### sentinel 整合 ribbon+openFeign+fallback

1. 修改 84 模块,使其支持 feign

   1. pom

      ![](media/sentinel%E7%9A%84%E7%9A%8423.png)

   2. 配置文件

      ![](media/sentinel%E7%9A%84%E7%9A%8424.png)

   3. 主启动类,也要修改

      ![](media/sentinel%E7%9A%84%E7%9A%8425.png)

   4. 创建远程调用 pay 模块的接口

      ![](media/sentinel%E7%9A%84%E7%9A%8426.png)

   5. 创建这个接口的实现类,用于降级

      ![](media/sentinel%E7%9A%84%E7%9A%8427.png)

   6. 再次修改接口,指定降级类

      ![](media/sentinel%E7%9A%84%E7%9A%8428.png)

   7. controller 添加远程调用

      ![](media/sentinel%E7%9A%84%E7%9A%8429.png)

   8. 测试

      启动 9003,84

   9. 测试,如果关闭 9003.看看 84 会不会降级

      ![](media/sentinel%E7%9A%84%E7%9A%8430.png)

      **可以看到,正常降级了**

**熔断框架比较**

![](media/sentinel%E7%9A%84%E7%9A%8431.png)

### sentinel 持久化规则

默认规则是临时存储的,重启 sentinel 就会消失

![](media/sentinel%E7%9A%84%E7%9A%8432.png)

**这里以之前的 8401 为案例进行修改:**

1. 修改 8401 的 pom

   ```xml
   添加:
   <!-- SpringCloud ailibaba sentinel-datasource-nacos 持久化需要用到-->
   <dependency>
       <groupId>com.alibaba.csp</groupId>
       <artifactId>sentinel-datasource-nacos</artifactId>
   </dependency>

   ```

2. 修改配置文件:

   添加:

   ![](media/sentinel%E7%9A%84%E7%9A%8433.png)

   **实际上就是指定,我们的规则要保证在哪个名称空间的哪个分组下**

   这里没有指定 namespace, 但是是可以指定的

   **注意,这里的 dataid 要与 8401 的服务名一致**

3. **在 nacos 中创建一个配置文件,dataId 就是上面配置文件中指定的**

   ![](media/sentinel%E7%9A%84%E7%9A%8434.png)

   ==json 中,这些属性的含义:==

   ![](media/sentinel%E7%9A%84%E7%9A%8435.png)

4. 启动 8401:

   ![](media/sentinel%E7%9A%84%E7%9A%8436.png)

   可以看到,直接读取到了规则

5. 关闭 8401

   ![](media/sentinel%E7%9A%84%E7%9A%8437.png)

6. 此时重启 8401,如果 sentinel 又可以正常读取到规则,那么证明持久化成功

   可以看到,又重新出现了

   ![](media/sentinel%E7%9A%84%E7%9A%8438.png)

# Seata

是一个分布式事务的解决方案

## 概念

**分布式事务中的一些概念,也是 seata 中的概念:**

Transaction ID XID 全局唯一的事务 ID

三组件：

TC（事务协调者）：维护全局和分子事务的状态，驱动全局事务提交或回滚。

TM（事务管理器）：定义全局事务的范围，开始全局事务，提交或回滚全局事务。

RM（资源管理器）：管理分支事务处理的资源，与 TC 交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

![](media/seala%E7%9A%843.png)

## seata 安装

（1)：**下载安装 seata 的安装包**

（2）：**修改 file.conf**

```conf
service {
									# 这个名字可以随便取
	vgroup_mapping.my_test_tx_group ="fsp_tx_group"
	default.grouplist = "127.0.0.1:8091"
	enableDegrade = false
	disable = false
	max.commit.retry.timeout = "-1"max.rollback.retry.timeout = "-1"}
store {
	## store mode: file、db
	mode = "db”
	## 表示使用数据库存储事务信
	db {
		## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
		datasource = "dbcp"
		## mysql/oracle/h2/oceanbase etc.
		db-type = "mysql"
		driver-class-name = "com.mysql.jdbc.Driver"
		url = "jdbc:mysql://127.0.0.1:3306/seata"
		user = "root
		## 指定mysql相关配置
		password ="你自己密码”l
		min-conn = 1
		max-conn = 3
		global.table = "global_table"
		branch.table = "branch_table"
		lock-table = "lock_table"
		query-limit = 100
		}

```

（3）：**mysql 建库建表**

上面指定了数据库为 seata,所以创建一个数据库名为 seata

建表,在 seata 的安装目录下有一个 db_store.sql,运行即可

**继续修改配置文件,修改 registry.conf**

配置 seata 作为微服务,指定注册中心

```properties
registry{
	# file . nacos . eureka、redis、zk、
	type = "nacos"
	# 指定注册中心为nacos
	nacos {
		# 指定nacos地址
		serverAddr = "localhost:8848"
		namespace = ""
		cluster = "default"
	}
```

启动

先启动 nacos

在启动 seata-server(运行安装目录下的,seata-server.bat)

**业务说明**：

我们创建三个服务，一个订单服务，一个库存服务，一个账户服务

当用户下单时，会在订单服务中创建一个订单，然后通过远程调用库存服务来扣减下单商品的库存，

再通过远程调用账户服务来扣减用户账户里面的余额

最后在订单服务中修改订单状态为已完成。

下单--->库存--->账号余额

1：创建三个数据库

seata_order：存储订单的数据库

seata_storage：存储库存的数据库

seata_account：存储账户信息的数据库

2：创建对应的表

seata_order：建 t_order 表

seata_storage：建 t_storage 表

seata_account：建 t_account 表

3：创建回滚日志表,方便查看

![](media/seala%E7%9A%8411.png)

**注意==每个库都要执行一次==这个 sql,生成回滚日志表**

==每个业务都创建一个微服务,也就是要有三个微服务,订单,库存,账号==

==订单==,seta-order-2001

1.  pom

2.  配置文件

    ```yaml
    server:
      port: 2001

    spring:
      application:
        name: seata-order-service
      cloud:
        alibaba:
          seata:
            # 自定义事务组名称需要与seata-server中的对应,我们之前在seata的配置文件中配置的名字
            tx-service-group: fsp_tx_group
        nacos:
          discovery:
            server-addr: 127.0.0.1:8848
      datasource:
        # 当前数据源操作类型
        type: com.alibaba.druid.pool.DruidDataSource
        # mysql驱动类
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/seata_order?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8
        username: root
        password: root
    feign:
      hystrix:
        enabled: false
    logging:
      level:
        io:
          seata: info

    mybatis:
      mapperLocations: classpath*:mapper/*.xml
    ```

    还要额外创建其他配置文件,创建一个 file.conf:

    ```.conf
    transport {
     # tcp udt unix-domain-socket
     type = "TCP"
     #NIO NATIVE
     server = "NIO"
     #enable heartbeat
     heartbeat = true
     #thread factory for netty
     thread-factory {
       boss-thread-prefix = "NettyBoss"
       worker-thread-prefix = "NettyServerNIOWorker"
       server-executor-thread-prefix = "NettyServerBizHandler"
       share-boss-worker = false
       client-selector-thread-prefix = "NettyClientSelector"
       client-selector-thread-size = 1
       client-worker-thread-prefix = "NettyClientWorkerThread"
       # netty boss thread size,will not be used for UDT
       boss-thread-size = 1
       #auto default pin or 8
       worker-thread-size = 8
     }
     shutdown {
       # when destroy server, wait seconds
       wait = 3
     }
     serialization = "seata"
     compressor = "none"
    }
    service {
     #vgroup->rgroup
     # 事务组名称
     vgroup_mapping.fsp_tx_group = "default"
     #only support single node
     default.grouplist = "127.0.0.1:8091"
     #degrade current not support
     enableDegrade = false
     #disable
     disable = false
     #unit ms,s,m,h,d represents milliseconds, seconds, minutes, hours, days, default permanent
     max.commit.retry.timeout = "-1"
     max.rollback.retry.timeout = "-1"
    }

    client {
     async.commit.buffer.limit = 10000
     lock {
       retry.internal = 10
       retry.times = 30
     }
     report.retry.count = 5
     tm.commit.retry.count = 1
     tm.rollback.retry.count = 1
    }

    ## transaction log store
    store {
     ## store mode: file、db
     #mode = "file"
     mode = "db"

     ## file store
     file {
       dir = "sessionStore"

       # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
       max-branch-session-size = 16384
       # globe session size , if exceeded throws exceptions
       max-global-session-size = 512
       # file buffer size , if exceeded allocate new buffer
       file-write-buffer-cache-size = 16384
       # when recover batch read size
       session.reload.read_size = 100
       # async, sync
       flush-disk-mode = async
     }

     ## database store
     db {
       ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
       datasource = "dbcp"
       ## mysql/oracle/h2/oceanbase etc.
       db-type = "mysql"
       driver-class-name = "com.mysql.jdbc.Driver"
       url = "jdbc:mysql://127.0.0.1:3306/seata"
       user = "root"
       password = "root"
       min-conn = 1
       max-conn = 3
       global.table = "global_table"
       branch.table = "branch_table"
       lock-table = "lock_table"
       query-limit = 100
     }
    }
    lock {
     ## the lock store mode: local、remote
     mode = "remote"

     local {
       ## store locks in user's database
     }

     remote {
       ## store locks in the seata's server
     }
    }
    recovery {
     #schedule committing retry period in milliseconds
     committing-retry-period = 1000
     #schedule asyn committing retry period in milliseconds
     asyn-committing-retry-period = 1000
     #schedule rollbacking retry period in milliseconds
     rollbacking-retry-period = 1000
     #schedule timeout retry period in milliseconds
     timeout-retry-period = 1000
    }

    transaction {
     undo.data.validation = true
     undo.log.serialization = "jackson"
     undo.log.save.days = 7
     #schedule delete expired undo_log in milliseconds
     undo.log.delete.period = 86400000
     undo.log.table = "undo_log"
    }

    ## metrics settings
    metrics {
     enabled = false
     registry-type = "compact"
     # multi exporters use comma divided
     exporter-list = "prometheus"
     exporter-prometheus-port = 9898
    }

    support {
     ## spring
     spring {
       # auto proxy the DataSource bean
       datasource.autoproxy = false
     }
    }

    ```

    创建 registry.conf:

    ```conf
    registry {
      # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
      type = "nacos"

      nacos {
        #serverAddr = "localhost"
        serverAddr = "localhost:8848"
        namespace = ""
        cluster = "default"
      }
      eureka {
        serviceUrl = "http://localhost:8761/eureka"
        application = "default"
        weight = "1"
      }
      redis {
        serverAddr = "localhost:6379"
        db = "0"
      }
      zk {
        cluster = "default"
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      consul {
        cluster = "default"
        serverAddr = "127.0.0.1:8500"
      }
      etcd3 {
        cluster = "default"
        serverAddr = "http://localhost:2379"
      }
      sofa {
        serverAddr = "127.0.0.1:9603"
        application = "default"
        region = "DEFAULT_ZONE"
        datacenter = "DefaultDataCenter"
        cluster = "default"
        group = "SEATA_GROUP"
        addressWaitTime = "3000"
      }
      file {
        name = "file.conf"
      }
    }

    config {
      # file、nacos 、apollo、zk、consul、etcd3
      type = "file"

      nacos {
        serverAddr = "localhost"
        namespace = ""
      }
      consul {
        serverAddr = "127.0.0.1:8500"
      }
      apollo {
        app.id = "seata-server"
        apollo.meta = "http://192.168.1.204:8801"
      }
      zk {
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      etcd3 {
        serverAddr = "http://localhost:2379"
      }
      file {
        name = "file.conf"
      }
    }

    ```

    ==实际上,就是要将 seata 中的我们之前修改的两个配置文件复制到这个项目下==

3.  **主启动类**

    ```java
    @SpringBootApplication(exclude = DataSourceAutoConfiguration.class) //取消数据源的自动创建
    @EnableDiscoveryClient
    @EnableFeignClients
    public class SeataOrderMain2001 {

        public static void main(String[] args) {
            SpringApplication.run(SeataOrderMain2001.class,args);
        }
    }
    ```

4.  **service 层**

    ```xml
    public interface OrderService {

        /**
         * 创建订单
         * @param order
         */
        void create(Order order);
    }
    ```

    ```xml
    @FeignClient(value = "seata-storage-service")
    public interface StorageService {

        /**
         * 减库存
         * @param productId
         * @param count
         * @return
         */
        @PostMapping(value = "/storage/decrease")
        CommonResult decrease(@RequestParam("productId") Long productId, @RequestParam("count") Integer count);
    }
    ```

    ```xml
    @FeignClient(value = "seata-account-service")
    public interface AccountService {

        /**
         * 减余额
         * @param userId
         * @param money
         * @return
         */
        @PostMapping(value = "/account/decrease")
        CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
    }
    ```

    ````

    ​```xml
    @Service
    @Slf4j
    public class OrderServiceImpl implements OrderService {

        @Resource
        private OrderDao orderDao;
        @Resource
        private AccountService accountService;
        @Resource
        private StorageService storageService;

        /**
         * 创建订单->调用库存服务扣减库存->调用账户服务扣减账户余额->修改订单状态
         * 简单说:
         * 下订单->减库存->减余额->改状态
         * GlobalTransactional seata开启分布式事务,异常时回滚,name保证唯一即可
         * @param order 订单对象
         */
        @Override
        ///@GlobalTransactional(name = "fsp-create-order", rollbackFor = Exception.class)
        public void create(Order order) {
            // 1 新建订单
            log.info("----->开始新建订单");
            orderDao.create(order);

            // 2 扣减库存
            log.info("----->订单微服务开始调用库存,做扣减Count");
            storageService.decrease(order.getProductId(), order.getCount());
            log.info("----->订单微服务开始调用库存,做扣减End");

            // 3 扣减账户
            log.info("----->订单微服务开始调用账户,做扣减Money");
            accountService.decrease(order.getUserId(), order.getMoney());
            log.info("----->订单微服务开始调用账户,做扣减End");

            // 4 修改订单状态,从0到1,1代表已完成
            log.info("----->修改订单状态开始");
            orderDao.update(order.getUserId(), 0);

            log.info("----->下订单结束了,O(∩_∩)O哈哈~");
        }
    }
    ````

5.  **dao 层,也就是接口**

    ```java
    @Mapper
    public interface OrderDao {
        /**
         * 1 新建订单
         * @param order
         * @return
         */
        int create(Order order);

        /**
         * 2 修改订单状态,从0改为1
         * @param userId
         * @param status
         * @return
         */
        int update(@Param("userId") Long userId, @Param("status") Integer status);
    }
    ```

    ==在 resource 下创建 mapper 文件夹,编写 mapper.xml==

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.eiletxie.springcloud.alibaba.dao.OrderDao">

        <resultMap id="BaseResultMap" type="com.eiletxie.springcloud.alibaba.domain.Order">
            <id column="id" property="id" jdbcType="BIGINT"></id>
            <result column="user_id" property="userId" jdbcType="BIGINT"></result>
            <result column="product_id" property="productId" jdbcType="BIGINT"></result>
            <result column="count" property="count" jdbcType="INTEGER"></result>
            <result column="money" property="money" jdbcType="DECIMAL"></result>
            <result column="status" property="status" jdbcType="INTEGER"></result>
        </resultMap>

        <insert id="create" parameterType="com.eiletxie.springcloud.alibaba.domain.Order" useGeneratedKeys="true"
                keyProperty="id">
            insert into t_order(user_id,product_id,count,money,status) values (#{userId},#{productId},#{count},#{money},0);
        </insert>

        <update id="update">
            update t_order set status =1 where user_id =#{userId} and status=#{status};
       </update>
    </mapper>

    ```

6.  **controller 层**

    ```java
    @RestController
    public class OrderController {
        @Resource
        private OrderService orderService;
    ```

        /**
         * 创建订单
         *
         * @param order
         * @return
         */
        @GetMapping("/order/create")
        public CommonResult create(Order order) {
            orderService.create(order);
            return new CommonResult(200, "订单创建成功");
        }

    }

    ```

    ```

7.  **entity 类(也叫 domain 类)**

    ```java
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class CommonResult<T> {
        private Integer code;
        private String message;
        private T data;

        public CommonResult(Integer code, String message) {
            this(code, message, null);
        }
    }

    ```

    ![](media/seala%E7%9A%8412.png)

8.  config 配置类

    ```java
    @Configuration
    @MapperScan({"com.eiletxie.springcloud.alibaba.dao"})		指定我们的接口的位置
    public class MyBatisConfig {
    ```

    }

    ````

    ​```java

    /**
     * @Author EiletXie
     * @Since 2020/3/18 21:51
     * 使用Seata对数据源进行代理
     */
    @Configuration
    public class DataSourceProxyConfig {

        @Value("${mybatis.mapperLocations}")
        private String mapperLocations;

        @Bean
        @ConfigurationProperties(prefix = "spring.datasource")
        public DataSource druidDataSource() {
            return new DruidDataSource();
        }

        @Bean
        public DataSourceProxy dataSourceProxy(DataSource druidDataSource) {
            return new DataSourceProxy(druidDataSource);
        }

        @Bean
        public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
            SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
            bean.setDataSource(dataSourceProxy);
            ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
            bean.setMapperLocations(resolver.getResources(mapperLocations));
            return bean.getObject();
        }
    }
    ````

    ```

    ```

9.

10.

11.

==库存==,seta-storage-2002

**==看脑图==**

1. pom
2. 配置文件
3. 主启动类
4. service 层
5. dao 层
6. controller 层 7. 8.

==账号==,seta-account-2003

**==看脑图==**

1. pom
2. 配置文件
3. 主启动类
4. service 层
5. dao 层
6. controller 层 7. 8.

7. **全局创建完成后,首先测试不加 seata**

   ![](media/seala%E7%9A%8414.png)

   ![](media/seala%E7%9A%8413.png)

8. 使用 seata:

   **在==订单模块==的 serviceImpl 类中的==create 方法==添加启动分布式事务的注解**

   ```java
   /**
   	这里添加开启分布式事务的注解,name指定当前全局事务的名称
   	rollbackFor表示,发生什么异常需要回滚
   	noRollbackFor:表示,发生什么异常不需要回滚
   */
   @GlobalTransactional(name = "fsp-create-order",rollbackFor = Exception.class)
   ///@GlobalTransactional(name = "fsp-create-order", rollbackFor = Exception.class)
   public void create(Order order) {
       // 1 新建订单
       log.info("----->开始新建订单");
       orderDao.create(order);

       // 2 扣减库存
       log.info("----->订单微服务开始调用库存,做扣减Count");
       storageService.decrease(order.getProductId(), order.getCount());
       log.info("----->订单微服务开始调用库存,做扣减End");

       // 3 扣减账户
       log.info("----->订单微服务开始调用账户,做扣减Money");
       accountService.decrease(order.getUserId(), order.getMoney());
       log.info("----->订单微服务开始调用账户,做扣减End");

       // 4 修改订单状态,从0到1,1代表已完成
       log.info("----->修改订单状态开始");
       orderDao.update(order.getUserId(), 0);

       log.info("----->下订单结束了,O(∩_∩)O哈哈~");
   }

   ```

9. 此时在测试

   发现,发生异常后,直接回滚了,前面的修改操作都回滚了

## seata 原理

![](media/seala%E7%9A%8415.png)

TM 开启分布式事务（TM 向 TC 注册全局事务记录）

按业务场景，编排数据库，服务等事务中资源（RM 向 TC 汇报资源准备状态）

TM 结束分布式事务，事务一阶段借宿（TM 通知 TC 提交/回滚分布式事务）

TC 汇总事务信息，决定分布式事务是提交还是回滚。

TC 通知所有 RM 提交/回滚资源，事务二阶段结束。

### seata 提供了四个模式

AT，TCC，SAGA 和 XA 事务模式

AT 模式：

前提

- 基于支持本地 ACID 事务的关系型数据库
- Java 应用，通过 JDBC 访问数据库

整体机制：

两阶段提交协议的演变

- 一阶段：业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源。

- 二阶段：

  提交异步化，非常快速地完成

  回滚通过一阶段的回滚日志进行反向补偿。

==第一阶段:==

![](media/seala%E7%9A%8420.png)

在一阶段，Seata 会拦截业务 SQL

1：解析 SQL 语义，找到业务 SQL 要更新的业务数据，在业务数据被更新前，将其保存为“before image”也就是将元数据保存一份，方便回滚。

2：执行业务 SQL 更新业务数据，在业务数据更新之后，更新后的数据也保存一份

3：其保存成“after image”，最后生成行锁

以上操作全部在一个数据库事务内完成，这样就保证了一阶段操作的原子性。

==二阶段之提交==:

![](media/seala%E7%9A%8421.png)

==二阶段之回滚:==

二阶段如果是回滚的话，Seata 就需要回滚一阶段已经执行的业务 SQL，还原业务数据，

回滚方式便是用“before image“还原业务数据，但在还原铅要首先校检脏写，对比”数据库当前业务数据“和”after image“如果两份数据完全一致就说明没有脏写，可以还原业务数据，如果不一致就说明有脏写，出现脏写就需要转人工处理。

![](media/seala%E7%9A%8423.png)

==断点==:

![](media/seala%E7%9A%8424.png)

**可以看到,他们的 xid 全局事务 id 是一样的,证明他们在一个事务下**

![](media/seala%E7%9A%8425.png)

**before 和 after 的原理就是**

```sql
select age from t where id =1 ; age =22
update set age = 28 where id =1;
select age from t where id = 1; age =28
```

**在更新数据之前,先解析这个更新 sql,然后查询要更新的数据,进行保存**
