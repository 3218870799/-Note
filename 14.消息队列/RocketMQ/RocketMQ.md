# 一：下载安装

1：window

2：linux

目录

* bin：启动脚本，包括shell脚本和CMD脚本
* conf：实例配置文件 ，包括broker配置文件、logback配置文件等
* lib：依赖jar包，包括Netty、commons-lang、FastJSON等

一：启动MQ

1：启动NameServer

```shell
# 1.启动NameServer
nohup sh bin/mqnamesrv &
# 2.查看启动日志
tail -f ~/logs/rocketmqlogs/namesrv.log
```

2：启动Broker

```shell
# 1.启动Broker
nohup sh bin/mqbroker -n localhost:9876 &
# 2.查看启动日志
tail -f ~/logs/rocketmqlogs/broker.log 
```

RocketMQ默认的虚拟机内存较大，启动Broker如果内存不足失败，需要编辑如下两个配置文件，修改JVM内存大小；

```shell
# 编辑runbroker.sh和runserver.sh修改默认JVM大小
vi runbroker.sh
vi runserver.sh
```

* 参考设置：

```properties
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m -XX:MetaspaceSize=128m  -XX:MaxMetaspaceSize=320m“
```

二：测试

1：发送消息

```sh
# 1.设置环境变量
export NAMESRV_ADDR=localhost:9876
# 2.使用安装包的Demo发送消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
```

2：接受消息

```shell
# 1.设置环境变量
export NAMESRV_ADDR=localhost:9876
# 2.接收消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
```

三：关闭RocketMQ

```shell
# 1.关闭NameServer
sh bin/mqshutdown namesrv
# 2.关闭Broker
sh bin/mqshutdown broker
```

# 二：集群搭建

各角色介绍

* Producer：消息的发送者；举例：发信者
* Consumer：消息接收者；举例：收信者
* Broker：暂存和传输消息；举例：邮局
* NameServer：管理Broker；举例：各个邮局的管理机构
* Topic：区分消息的种类；一个发送者可以发送消息给一个或者多个Topic；一个消息的接收者可以订阅一个或者多个Topic消息
* Message Queue：相当于是Topic的分区；用于并行发送和接收消息

模式：

1）单Master模式：

2）多Master模式：

3）多Master多Slave模式（异步）

4）多Master多Slave模式（同步）

## 双主双从服务搭建

工作流程

1. 启动NameServer，NameServer起来后监听端口，等待Broker、Producer、Consumer连上来，相当于一个路由控制中心。
2. Broker启动，跟所有的NameServer保持长连接，定时发送心跳包。心跳包中包含当前Broker信息(IP+端口等)以及存储所有Topic信息。注册成功后，NameServer集群中就有Topic跟Broker的映射关系。
3. 收发消息前，先创建Topic，创建Topic时需要指定该Topic要存储在哪些Broker上，也可以在发送消息时自动创建Topic。
4. Producer发送消息，启动时先跟NameServer集群中的其中一台建立长连接，并从NameServer中获取当前发送的Topic存在哪些Broker上，轮询从队列列表中选择一个队列，然后与队列所在的Broker建立长连接从而向Broker发消息。
5. Consumer跟Producer类似，跟其中一台NameServer建立长连接，获取当前订阅Topic存在哪些Broker上，然后直接跟Broker建立连接通道，开始消费消息。

## mqadmin管理工具

进入RocketMQ安装位置，在bin目录下执行` ./mqadmin {command} {args} ` 

1：topic相关

| 名称 | 含义 | 命令选项 | 说明 |
| ---- | ---- | -------- | ---- |
|      |      |          |      |
|      |      |          |      |
|      |      |          |      |

5：消费者，消费者组相关

6：连接相关

7：NameServer相关

8：其他



几乎所有命令都需要配置 -n 表示NameServer地址，格式为ip：port

几乎所有命令都可以通过-h获取帮助

如果既有Broker地址（-b) 配置项又有clusterName（-c)配置项，则优先对Broker地址执行命令；如果不配置Broker地址，则对集群中所有主机执行命令

## 集群监控平台搭建

`RocketMQ`有一个对其扩展的开源项目[incubator-rocketmq-externals](https://github.com/apache/rocketmq-externals)，这个项目中有一个子模块叫`rocketmq-console`，这个便是管理控制台项目了，先将[incubator-rocketmq-externals](https://github.com/apache/rocketmq-externals)拉到本地，因为我们需要自己对`rocketmq-console`进行编译打包运行。



![](media/rocketmq-console.png)

下载并编译打包

```sh
git clone https://github.com/apache/rocketmq-externals
cd rocketmq-console
mvn clean package -Dmaven.test.skip=true
```

注意：打包前在```rocketmq-console```中配置```namesrv```集群地址：

```sh
rocketmq.config.namesrvAddr=192.168.25.135:9876;192.168.25.138:9876
```

启动rocketmq-console：

```sh
java -jar rocketmq-console-ng-1.0.0.jar
```

启动成功后，我们就可以通过浏览器访问`http://localhost:8080`进入控制台界面了，如下图：

![](media/rocketmq-console2.png)

集群状态：

![](media/rocketmq-console3.png)

# 三：消息发送

