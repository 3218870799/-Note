# 一：简介

Zookeeper 是一个开源的分布式的，为分布式应用提供协调服务的 Apache 项目。是 Hadoop 和 HBase 的重要组件。

提供的服务包括：文件系统和通知机制。

统一命名服务、统一配置管理、统一集群管理、服务器节点动态上下线、软负载均衡等。

可以实现：数据发布订阅，负载均衡，分布式协调通知，集群管理，……

统一命名服务

## 数据模型

ZooKeeper 中的数据模型是一种树形结构，非常像电脑中的文件系统，有一个根文件夹，下面还有很多子文件夹。

- ZooKeeper的数据模型也具有一个固定的根节点`（/）`，我们可以在根节点下创建子节点，并在子节点下继续创建下一级节点。
- ZooKeeper 树中的每一层级用斜杠`（/）`分隔开，且只能用绝对路径（如`get /work/task`）的方式查询 ZooKeeper 节点，而不能使用相对路径。

![图片](https://mmbiz.qpic.cn/mmbiz_png/hC3oNAJqSRyZiabcsEQJjF6lw5EQMxj7ncQqsBGn1BTbg0odhYemnGyqygEbFiamMKGN7picQLyHvLLnxS8El4uaQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

1：节点类型

四种数据节点 ZNode：

（1）persistent-持久节点

除非手动删除，否则节点一直存在于 Zookeeper 上

（2）ephemeral-临时节点

临时节点的生命周期与**客户端会话绑定**，一旦客户端会话失效（客户端与 zookeeper 连接断开不一定会话失效），那么这个客户端创建的所有临时节点都会被移除。**不允许有子节点。**

（3）persistent_sequential-持久顺序节点

基本特性同持久节点，只是增加了顺序属性，节点名后边会追加一个由父节点维护的自增整型数字。类似 mysql 的 id，不允许重名节点

（4）ephemeral_sequential-临时顺序节点

基本特性同临时节点，增加了顺序属性，节点名后边会追加一个由父节点维护的自增整型数字。

ZNode具有原子性操作，存储数据大小有限制；



2：节点的状态结构

每一个节点都有一个自己的状态属性，记录了节点本身的一些信息：

| **「状态属性」** | **「说明」**                                                 |
| :--------------- | :----------------------------------------------------------- |
| czxid            | 数据节点创建时的事务 ID                                      |
| ctime            | 数据节点创建时的时间                                         |
| mzxid            | 数据节点最后一次更新时的事务 ID                              |
| mtime            | 数据节点最后一次更新时的时间                                 |
| pzxid            | 数据节点的子节点最后一次被修改时的事务 ID                    |
| **「cversion」** | **「子节点的版本」**                                         |
| **「version」**  | **「当前节点数据的版本」**                                   |
| **「aversion」** | **「节点的 ACL 的版本」**                                    |
| ephemeralOwner   | 如果节点是临时节点，则表示创建该节点的会话的 SessionID；如果节点是持久节点，则该属性值为 0 |
| dataLength       | 数据内容的长度                                               |
| numChildren      | 数据节点当前的子节点个数                                     |

czxid-创建节点的事务 zxid 每次修改 ZooKeeper 状态都会收到一个 zxid 形式的时间戳，也就是 ZooKeeper 事务 ID。事务 ID 是 ZooKeeper 中所有修改总的次序。每个修改都有唯一的 zxid，如果 zxid1 小于 zxid2，那么 zxid1 在 zxid2 之前发生。

## 特点

最终一致性

半数存活：集群中只要有半数以上节点存活，Zookeeper 集群就能正常服务。

1）Zookeeper：一个领导者（leader），多个跟随者（follower）组成的集群。

2）Leader 负责进行投票的发起和决议，更新系统状态

3）Follower 用于接收客户请求并向客户端返回结果，在选举 Leader 过程中参与投票

5）全局数据一致：每个 server 保存一份相同的数据副本，client 无论连接到哪个 server，数据都是一致的。

6）更新请求顺序进行，来自同一个 client 的更新请求按其发送顺序依次执行。

7）数据更新原子性，一次数据更新要么成功，要么失败。

8）实时性，在一定时间范围内，client 能读到最新数据。



## 作用

命名服务；

配置管理；

集群管理



# 二：安装

## 下载地址

1．官网首页： https://zookeeper.apache.org/

## 本地模式安装部署

1．安装前准备

安装 Jdk

拷贝 Zookeeper 安装包到 Linux 系统下

解压到指定目录

```shell
tar 3.4.10.tar.gz -C /opt/module/ | -zxvf zookeeper- |
```

2．配置修改

将/opt/module/zookeeper-3.4.10/conf 这个路径下的 zoo_sample.cfg 修改为 zoo.cfg；

打开 zoo.cfg 文件，修改 dataDir 路径：

```txt
dataDir=/opt/module/zookeeper-3.4.10/zkData
```

在/opt/module/zookeeper-3.4.10/这个目录上创建 zkData 文件夹

```shell
mkdir zkData
```

3．操作 Zookeeper

启动 Zookeeper

```shell
bin/zkServer.sh start
```

查看进程是否启动：jps

查看状态：

启动客户端：

```shell
bin/zkCli.sh
```

退出客户端：

停止 Zookeeper

```shell
bin/zkServer.sh stop
```

## 配置参数解读

Zookeeper 中的配置文件 zoo.cfg 中参数含义解读如下：

1．tickTime =2000：通信心跳数，Zookeeper 服务器与客户端心跳时间，单位毫秒

Zookeeper 使用的基本时间，服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个 tickTime 时间就会发送一个心跳，时间单位为毫秒。

它用于心跳机制，并且设置最小的 session 超时时间为两倍心跳时间。(session 的最小超时时间是 2\*tickTime)

2．initLimit =10：LF 初始通信时限

集群中的 Follower 跟随者服务器与 Leader 领导者服务器之间初始连接时能容忍的最多心跳数（tickTime 的数量），用它来限定集群中的 Zookeeper 服务器连接到 Leader 的时限。

3．syncLimit =5：LF
同步通信时限集群中 Leader 与 Follower 之间的最大响应时间单位，假如响应超过 syncLimittickTime，Leader 认为 Follwer 死掉，从服务器列表中删除 Follwer。

4．dataDir：数据文件目录+数据持久化路径主要用于保存 Zookeeper 中的数据。

5．clientPort =2181：客户端连接端口监听客户端连接的端口。

# 第 3 章 应用

## 集群

在 ZooKeeper 集群中将服务器分成 「Leader 、Follow 、Observer」三种角色服务器，在集群运行期间这三种服务器所负责的工作各不相同：

- Leader 角色服务器负责管理集群中其他的服务器，是集群中工作的分配和调度者，既可以为客户端提供写服务又能提供读服务。
- Follow 服务器的主要工作是选举出 Leader 服务器，在发生 Leader 服务器选举的时候，系统会从 Follow 服务器之间根据多数投票原则，选举出一个 Follow 服务器作为新的 Leader 服务器，只能提供读服务。
- Observer 服务器则主要负责处理来自客户端的获取数据等请求，并不参与 Leader 服务器的选举操作，也不会作为候选者被选举为 Leader 服务器，只能提供读服务。

在 ZooKeeper 集群接收到来自客户端的会话请求操作后，首先会判断该条请求是否是事务性的会话请求。

对于事务性的会话请求，ZooKeeper 集群服务端会将该请求统一转发给 Leader 服务器进行操作。

Leader 服务器内部执行该条事务性的会话请求后，再将数据同步给其他角色服务器，从而保证事务性会话请求的执行顺序，进而保证整个 ZooKeeper 集群的数据一致性。

如果不是事务性请求，ZooKeeper 集群则交由 Follow 和 Observer 角色服务器处理该条会话请求，如查询数据节点信息。

### **1：Leader选举**

Leader 服务器的选举操作主要发生在两种情况下。

第一种就是 ZooKeeper 集群服务启动的时候，第二种就是在 ZooKeeper 集群中旧的 Leader 服务器失效时。

ZooKeeper 集群重新选举 Leader 的过程只有 Follow 服务器参与工作。

服务器具有四种状态，分别是 LOOKING、FOLLOWING、LEADING、OBSERVING。

- LOOKING：寻找 Leader 状态。当服务器处于该状态时，它会认为当前集群中没有 Leader，因此需要进入 Leader 选举状态。
- FOLLOWING：跟随者状态。表明当前服务器角色是 Follower。
- LEADING：领导者状态。表明当前服务器角色是 Leader。
- OBSERVING：观察者状态。表明当前服务器角色是 Observer。

选举过程：

1：失效发现

当 Follow 服务器向 Leader 服务器发送状态请求包后，如果没有得到 Leader 服务器的返回信息，这时，如果是集群中个别的 Follow 服务器发现返回错误，并不会导致 ZooKeeper 集群立刻重新选举 Leader 服务器，而是将该 Follow 服务器的状态变更为 LOOKING 状态，并向网络中发起投票，当 ZooKeeper 集群中有更多的机器发起投票，最后当投票结果满足多数原则的情况下，ZooKeeper 会重新选举出 Leader 服务器。

2：重新选举

3：角色变更

4：集群数据同步

以一个简单的例子来说明整个选举的过程。

假设有五台服务器组成的 Zookeeper 集群，它们的 id 从 1-5，同时它们都是最新启动的，也就是没有历史数据，在存放数据量这一点上，都是一样的。假设这些服务器依序启动，来看看会发生什么，如图

![](media/1cfe56f1c837c0e0c5155e28f2a2cafb.png)

1.  服务器 1 启动，发起一次选举。服务器 1 投自己一票。此时服务器 1 票数一票，不够半数以上（3 票），选举无法完成，服务器 1 状态保持为 LOOKING；
2.  服务器 2 启动，再发起一次选举。服务器 1 和 2 分别投自己一票并交换选票信息：此时服务器 1 发现服务器 2 的 ID 比自己目前投票推举的（服务器 1）大，更改选票为推举服务器 2。此时服务器 1 票数 0 票，服务器 2 票数 2 票，没有半数以上结果，选举无法完成，服务器 1，2 状态保持 LOOKING
3.  服务器 3 启动，发起一次选举。此时服务器 1 和 2 都会更改选票为服务器 3。此次投票结果：服务器 1 为 0 票，服务器 2 为 0 票，服务器 3 为 3 票。此时服务器 3 的票数已经超过半数，服务器 3 当选 Leader。服务器 1，2 更改状态为 FOLLOWING，服务器 3 更改状态为 LEADING；
4.  服务器 4 启动，发起一次选举。此时服务器 1，2，3 已经不是 LOOKING 状态，不会更改选票信息。交换选票信息结果：服务器 3 为 3 票，服务器 4 为 1 票。此时服务器 4 服从多数，更改选票信息为服务器 3，并更改状态为 FOLLOWING；
5.  服务器 5 启动，同 4 一样当小弟。

## 实现分布式配置中心

将集群上的配置文件统一进行管理到配置中心上，当某一处需要修改时，推送到集群的各个服务器上。

发布订阅

拉取方式：每隔一定时间主动拉取，不及时

推送方式：订阅某一项内容，当发生改变时，服务器推送，客户端注册 watch 监听器

- 这种方式存储数据量小，数据量要小于 1M
- 集群中各机器一致

## 实现软负载均衡

与 Nginx 的区别：

Nginx 等都是基于服务器端的负载均衡，Zookeeper 的负载均衡是基于客户端的负载均衡。

客户端存储服务器状态，类似王者荣耀让你选择每个区。让客户端主动选择服务器。

我们可以从客户端获取服务器的节点信息，根据负载均衡算法，选择一个服务器发送请求；



## 实现服务注册，治理与发现

## 实现分布式锁

**「方案一：」**

使用节点中的存储数据区域，ZK中节点存储数据的大小不能超过1M，但是只是存放一个标识是足够的，线程获得锁时，先检查该标识是否是无锁标识，若是可修改为占用标识，使用完再恢复为无锁标识

**「方案二：」**

使用子节点，每当有线程来请求锁的时候，便在锁的节点下创建一个子节点，子节点类型必须维护一个顺序，对子节点的自增序号进行排序，默认总是最小的子节点对应的线程获得锁，释放锁时删除对应子节点便可。



锁服务可以分为两类：一个是保持独占，另一个是控制时序

因为Zookeeper在创建节点的时候，需要保证节点的唯一性，也就是实现原理就是，每次一个线程获取到了锁，那就在Zookeeper上创建一个临时节点，但用完锁之后，在把这个节点删除掉

```
create /node v0410  # 创建一个持久节点
crate -e /node v0410 # 创建一个临时节点
```

对于单进程的并发场景，我们可以使用synchronized关键字和Reentrantlock等

对于 分布式场景，我们可以使用分布式锁。

创建锁

多个JVM服务器之间，同时在zookeeper上创建相同一个临时节点，因为临时节点路径是保证唯一，只要谁能创建节点成功，谁就能获取到锁。

没有创建成功节点，只能注册个监听器监听这个锁并进行等待，当释放锁的时候，采用事件通知给其它客户端重新获取锁的资源。

这时候客户端使用事件监听，如果该临时节点被删除的话，重新进入获取锁的步骤。

释放锁

Zookeeper使用直接关闭临时节点session会话连接，因为临时节点生命周期与session会话绑定在一块，如果session会话连接关闭，该临时节点也会被删除，这时候客户端使用事件监听，如果该临时节点被删除的话，重新进入到获取锁的步骤。

最后我们是具体的实现方法

```
/**
 * 分布式锁
 *
 */
public class ZkDistributedLock extends ZkAbstractTemplateLock{
    @Override
    public boolean tryLock() {
        // 判断节点是否存在，如果存在则返回false，否者返回true
        return false;
    }

    @Override
    public void waitZkLock() throws InterruptedException {
        // 等待锁的时候，需要加监控，查询这个lock是否释放

        CountDownLatch countDownLatch = new CountDownLatch(1);

        countDownLatch.await();

        // 解除监听
    }
```

然后我们通过ZkDistributedLock进行加锁

```
/**
 * 订单业务逻辑
 */
public class OrderService {
    private OrderNumberCreateUtil orderNumberCreateUtil = new OrderNumberCreateUtil();

    public void getOrderNumber() {
        ZkLock zkLock = new ZkDistributedLock();
        zkLock.zkLock();
        try {

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            zkLock.zkUnlock();
        }
        System.out.println(orderNumberCreateUtil.getOrderNumber());
    }
}
```

然后在使用多个线程进行操作，而且是在线程里面实例化对象，来进行创建，最终保证每个对象再获取订单的时候，都是唯一的

```
/**
 * 客户端
 */
public class Client {
    public static void main(String[] args) {
        for (int i = 0; i < 50; i++) {
            new Thread(() -> {
                new OrderService().getOrderNumber();
                System.out.println(str);
            }, String.valueOf(i)).start();
        }
    }
}
```

## 实现分布式ID





## 客户端命令行操作

| 命令基本语法     | 功能描述                                         |
| ---------------- | ------------------------------------------------ |
| help             | 显示所有操作命令                                 |
| ls path [watch]  | 使用 ls 命令来查看当前 znode 中所包含的内容      |
| ls2 path [watch] | 查看当前节点数据并能看到更新次数等数据           |
| create           | 普通创建 -s 含有序列 -e 临时（重启或者超时消失） |
| get path [watch] | 获得节点的值                                     |
| set              | 设置节点的具体值                                 |
| stat             | 查看节点状态                                     |
| delete           | 删除节点                                         |
| rmr              | 递归删除节点                                     |

1：创建节点

```shell
## 创建永久节点
create /test node
## 创建临时节点
create -e /test node
## 创建顺序节点
create -s /test
## 创建临时顺序节点
create -e -s /test
```



## 3.3 API 应用

### 3.3.1 环境搭建

1．创建一个 Maven 工程

2．添加 pom 文件

```xml
<dependencies>
  <dependency>
   <groupId>junit</groupId>
   <artifactId>junit</artifactId>
   <version>RELEASE</version>
  </dependency>
  <dependency>
   <groupId>org.apache.logging.log4j</groupId>
   <artifactId>log4j-core</artifactId>
   <version>2.8.2</version>
  </dependency>
  <!--
https://mvnrepository.com/artifact/org.apache.zookeeper/zook eeper -->
  <dependency>
   <groupId>org.apache.zookeeper</groupId>
   <artifactId>zookeeper</artifactId>
   <version>3.4.10</version>
  </dependency>
</dependencies>
```

3．拷贝 log4j.properties 文件到项目根目录需要在项目的 src/main/resources 目录下，新建一个文件，命名为“log4j.properties”，在文件中填入。

```yml
log4j.rootLogger=INFO, stdout   log4j.appender.stdout=org.apache.log4j.ConsoleAppender   log4j.appender.stdout.layout=org.apache.log4j.PatternLayout   log4j.appender.stdout.layout.ConversionPattern=%d %p [%c]
- %m%n
log4j.appender.logfile=org.apache.log4j.FileAppender   log4j.appender.logfile.File=target/spring.log   log4j.appender.logfile.layout=org.apache.log4j.PatternLayout   log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n
```

### **3.3.2** 创建 **ZooKeeper** 客户端

```java
private static String connectString =
 "hadoop102:2181,hadoop103:2181,hadoop104:2181";  private static int sessionTimeout = 2000;  private ZooKeeper zkClient = null;

 @Before
 public void init() throws Exception {

 zkClient = new ZooKeeper(connectString, sessionTimeout, new Watcher() {

   @Override
   public void process(WatchedEvent event) {

    // 收到事件通知后的回调函数（用户的业务逻辑）
    System.out.println(event.getType() + "--" + event.getPath());

    // 再次启动监听
    try {
     zkClient.getChildren("/", true);
    } catch (Exception e) {
     e.printStackTrace();
    }
   }
  });
 }
```

### 3.3.3 创建子节点

```java
// 创建子节点
@Test
public void create() throws Exception {

  // 参数1：要创建的节点的路径； 参数2：节点数据 ； 参数3：节点权限 ；参数4：节点的类型
  String nodeCreated = zkClient.create("/qcx",
	"jinlian".getBytes(), 	Ids.OPEN_ACL_UNSAFE,
CreateMode.PERSISTENT);
}
```

### **3.3.4** 获取子节点并监听节点变化

```java
// 获取子节点
@Test
public void getChildren() throws Exception {

  List<String> children = zkClient.getChildren("/", true);
  for (String child : children) {    System.out.println(child);
} 	}
// 延时阻塞
Thread.sleep(Long.MAX_VALUE);

```

### **3.3.5** 判断 **Znode** 是否存在

```java
// 判断znode是否存在
@Test
public void exist() throws Exception {

 Stat stat = zkClient.exists("/eclipse", false);
 System.out.println(stat == null ? "not exist" : "exist");
}
```

## 3.4 监听服务器节点动态上下线案例

1．需求某分布式系统中，主节点可以有多台，可以动态上下线，任意一台客户端都能实时感知到主节点服务器的上下线。

3．具体实现

先在集群上创建/servers 节点

```java
[zk: localhost:2181(CONNECTED) 10] create /servers "servers" Created /servers
```

服务器端向 Zookeeper 注册代码

```java

import java.io.IOException;
import org.apache.zookeeper.CreateMode; 
import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher; 
import org.apache.zookeeper.ZooKeeper; 
import org.apache.zookeeper.ZooDefs.Ids;

public class DistributeServer {

 private static String connectString="hadoop102:2181,hadoop103:2181,hadoop104:2181";  
 private static int sessionTimeout = 2000;  
 private ZooKeeper zk = null;
 private String parentNode = "/servers";

 // 创建到zk的客户端连接
 public void getConnect() throws IOException{
      zk = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
           @Override
           public void process(WatchedEvent event) {
           }
      });
 }

 // 注册服务器
 public void registServer(String hostname) throws Exception{
      String create = zk.create(parentNode+"/server",hostname.getBytes(),Ids.OPEN_ACL_UNSAFE,CreateMode.EPHEMERAL_SEQUENTIAL);

      System.out.println(hostname +" is online "+ create);
 }

 // 业务功能
 public void business(String hostname) throws Exception{
      System.out.println(hostname+" is working ...");

      Thread.sleep(Long.MAX_VALUE);
 }

 public static void main(String[] args) throws Exception {

	// 1获取zk连接
  	DistributeServer server = new DistributeServer();   
    server.getConnect();

     // 2 利用zk连接注册服务器信息
     server.registServer(args[0]);

      // 3 启动业务功能
      server.business(args[0]);
 }
```

1.  客户端代码

```java

import java.io.IOException; 
import java.util.ArrayList; 
import java.util.List;
import org.apache.zookeeper.WatchedEvent; 
import org.apache.zookeeper.Watcher; 
import org.apache.zookeeper.ZooKeeper;

public class DistributeClient {

 private static String connectString =
"hadoop102:2181,hadoop103:2181,hadoop104:2181";  
    private static int sessionTimeout = 2000;  
    private ZooKeeper zk = null;
    private String parentNode = "/servers";

 // 创建到zk的客户端连接
 public void getConnect() throws IOException {
  	zk = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
           @Override
           public void process(WatchedEvent event) {
                // 再次启动监听
                try {
                 getServerList();
                } catch (Exception e) {
                 e.printStackTrace();
                }
   			}
  	});
 }

 // 获取服务器列表信息
 public void getServerList() throws Exception {
  // 1获取服务器子节点信息，并且对父节点进行监听
  List<String> children = zk.getChildren(parentNode, true);

  // 2存储服务器信息列表
  ArrayList<String> servers = new ArrayList<>();

  // 3遍历所有节点，获取节点中的主机名称信息
  for (String child : children) {
       byte[] data = zk.getData(parentNode + "/" + child, false, null);
       servers.add(new String(data));
  }

     // 4打印服务器列表信息
     System.out.println(servers);
 }

 // 业务功能
 public void business() throws Exception{
  	System.out.println("client is working ..."); Thread.sleep(Long.MAX_VALUE);
 }

 public static void main(String[] args) throws Exception {

      // 1获取zk连接
      DistributeClient client = new DistributeClient();
      client.getConnect();

      // 2获取servers的子节点信息，从中获取服务器信息列表
      client.getServerList();

      // 3业务进程启动
      client.business();
 }
}

```

# 四：内部原理

## 监听器Watch

数据变更通知：

Zookeeper 允许客户端向服务端的某个 Znode 注册一个 Watcher 监听，当服务端的一些指定事件触发了这个 Watcher，服务端会向指定客户端发送一个事件通知来实现分布式的通知功能，然后客户端根据 Watcher 通知状态和事件类型做出业务上的改变。

工作机制：

（1）客户端注册 watcher

（2）服务端处理 watcher

（3）客户端回调 watcher



触发通知的条件：





Watcher 特性总结：

（1）一次性

无论是服务端还是客户端，一旦一个 Watcher 被 触 发 ，Zookeeper 都会将其从相应的存储中移除。这样的设计有效的减轻了服务端的压力，不然对于更新非常频繁的节点，服务端会不断的向客户端发送事件通知，无论对于网络还是服务端的压力都非常大。

（2）客户端串行执行

客户端 Watcher 回调的过程是一个串行同步的过程。

（3）轻量

3.1、Watcher 通知非常简单，只会告诉客户端发生了事件，而不会说明事件的具体内容。

3.2、客户端向服务端注册 Watcher 的时候，并不会把客户端真实的 Watcher 对象实体传递到服务端，仅仅是在客户端请求中使用 boolean 类型属性进行了标记。

（4）watcher event 异步发送 watcher 的通知事件从 server 发送到 client 是异步的，这就存在一个问题，不同的客户端和服务器之间通过 socket 进行通信，由于网络延迟或其他因素导致客户端在不通的时刻监听到事件，由于 Zookeeper 本身提供了 ordering guarantee，即客户端监听事件后，才会感知它所监视 znode 发生了变化。所以我们使用 Zookeeper 不能期望能够监控到节点每次的变化。Zookeeper 只能保证最终的一致性，而无法保证强一致性。

（5）注册 watcher getData、exists、getChildren

（6）触发 watcher create、delete、setData

（7）当一个客户端连接到一个新的服务器上时，watch 将会被以任意会话事件触发。当与一个服务器失去连接的时候，是无法接收到 watch 的。而当 client 重新连接时，如果需要的话，所有先前注册过的 watch，都会被重新注册。通常这是完全透明的。只有在一个特殊情况下，watch 可能会丢失：对于一个未创建的 znode 的 exist watch，如果在客户端断开连接期间被创建了，并且随后在客户端连接上之前又删除了，这种情况下，这个 watch 事件可能会被丢失。

#### 客户端注册 Watcher 实现

（1）调用 getData()/getChildren()/exist()三个 API，传入 Watcher 对象

（2）标记请求 request，封装 Watcher 到 WatchRegistration

（3）封装成 Packet 对象，发服务端发送 request

（4）收到服务端响应后，将 Watcher 注册到 ZKWatcherManager 中进行管理

（5）请求返回，完成注册。

#### 服务端处理 Watcher 实现

（1）服务端接收 Watcher 并存储

接收到客户端请求，处理请求判断是否需要注册 Watcher，需要的话将数据节点的节点路径和 ServerCnxn（ServerCnxn 代表一个客户端和服务端的连接，实现了 Watcher 的 process 接口，此时可以看成一个 Watcher 对象）存储在 WatcherManager 的 WatchTable 和 watch2Paths 中去。

（2）Watcher 触发

以服务端接收到 setData() 事务请求触发 NodeDataChanged 事件为例：

2.1 封装 WatchedEvent

将通知状态（SyncConnected）、事件类型（NodeDataChanged）以及节点路径封装成一个 WatchedEvent 对象

事件类型：WatchEvent.getType()

```java
## 节点创建
Event.EventType.NodeCreated 
## 删除节点
Event.EventType.NodeDeleted
```

2.2 查询 Watcher

从 WatchTable 中根据节点路径查找 Watcher

没找到；说明没有客户端在该数据节点上注册过 Watcher

找到；提取并从 WatchTable 和 Watch2Paths 中删除对应 Watcher（从这里可以看出 Watcher 在服务端是一次性的，触发一次就失效了）

调用 process 方法来触发 Watcher

这里 process 主要就是通过 ServerCnxn 对应的 TCP 连接发送 Watcher 事件通知。

#### 客户端回调 Watcher

客户端 SendThread 线程接收事件通知，交由 EventThread 线程回调 Watcher。

客户端的 Watcher 机制同样是一次性的，一旦被触发后，该 Watcher 就失效了。



## 会话机制

客户端要与服务端进行连接，而一个连接就是一个会话；

这个数据结构由三个部分组成：分别是会话 ID（sessionID）、会话超时时间（TimeOut）、会话关闭状态（isClosing）

会话状态：

正在连接（CONNECTING）、已经连接（CONNECTIED）、正在重新连接（RECONNECTING）、已经重新连接（RECONNECTED）、会话关闭（CLOSE）等。

会话异常：

会话的超时异常包括客户端 readtimeout 异常和服务器端 sessionTimeout 异常。

分桶策略：

在 ZooKeeper 中为了保证一个会话的存活状态，客户端需要向服务器周期性地发送心跳信息。

而客户端所发送的心跳信息可以是一个 ping 请求，也可以是一个普通的业务请求。

ZooKeeper 服务端接收请求后，会更新会话的过期时间，来保证会话的存活状态。

在 ZooKeeper 中，会话将按照不同的时间间隔进行划分，超时时间相近的会话将被放在同一个间隔区间中，这种方式避免了 ZooKeeper 对每一个会话进行检查，而是采用分批次的方式管理会话。



ZooKeeper 底层实现的原理，核心的一点就是过期队列这个数据结构。所有会话过期的相关操作都是围绕这个队列进行的。

可以说 ZooKeeper 底层就是采用这个队列结构来管理会话过期的。



5.  

## 访问机制ACL

一个 ACL 权限设置通常可以分为 3 部分，分别是：权限模式（Scheme）、授权对象（ID）、权限信息（Permission）。

最终组成一条例如`scheme:id:permission`格式的 ACL 请求信息。



创建Znode的时候，需要传入访问控制列表ACL，用于决定谁可以对它执行何种操作。

```txt
OPEN_ACL_UNSAFE  : 完全开放的ACL，任何连接的客户端都可以操作该属性znode,练习中使用的是
CREATOR_ALL_ACL : 只有创建者才有ACL权限
READ_ACL_UNSAFE：只能读取ACL
```

1：权限模式

- IP：从 IP 地址粒度进行权限控制
- Digest：最常用，通过用户名和密码识别客户端；
- World：最开放的权限控制方式，是一种特殊的 digest 模式，只有一个权限标识“world:anyone”
- Super：超级用户

2：授权对象

授权对象指的是权限赋予的用户或一个指定实体，例如 IP 地址或是机器灯。

3：权限信息

- CREATE：数据节点创建权限，允许授权对象在该 Znode 下创建子节点
- DELETE：子节点删除权限，允许授权对象删除该数据节点的子节点
- READ：数据节点的读取权限，允许授权对象访问该数据节点并读取其数据内容或子节点列表等
- WRITE：数据节点更新权限，允许授权对象对该数据节点进行更新操作
- ADMIN：数据节点管理权限，允许授权对象对该数据节点进行 ACL 相关设置操作



实现原理

首先是封装该请求的类型，之后将权限信息封装到 request 中并发送给服务端。而服务器的实现比较复杂，首先分析请求类型是否是权限相关操作，之后根据不同的权限模式（scheme）调用不同的实现类验证权限最后存储权限信息。

在授权接口中，值得注意的是会话的授权信息存储在 ZooKeeper 服务端的内存中，如果客户端会话关闭，授权信息会被删除。

下次连接服务器后，需要重新调用授权接口进行授权。



## ZAB 协议

ZooKeeper 最核心的作用就是保证分布式系统的数据一致性，而无论是处理来自客户端的会话请求时，还是集群 Leader 节点发生重新选举时，都会产生数据不一致的情况。

为了解决这个问题，ZooKeeper 采用了 ZAB 协议算法。

是 ZooKeeper 专门设计用来解决集群最终一致性问题的算法，

ZAB 协议包括两种基本的模式：崩溃恢复和消息广播。

当整个 zookeeper 集群刚刚启动或者 Leader 服务器宕机、重启或者网络故障导致不存在过半的服务器与 Leader 服务器保持正常通信时，所有进程（服务器）进入**崩溃恢复模式**，首先选举产生新的 Leader 服务器，然后集群中 Follower 服务器开始与新的 Leader 服务器进行数据同步，当集群中超过半数机器与该 Leader 服务器完成数据同步之后，退出恢复模式进**入消息广播模式**，Leader 服务器开始接收客户端的事务请求生成事物提案来进行事务请求处理。



## 写数据流程

# 第 5 章：常见问题与解决方案

**1：Zookeeper 怎么保证主从节点的状态同步？**



Zookeeper 的核心是原子广播机制，这个机制保证了各个 server 之间的同步。实现这个机制的协议叫做 Zab 协议。Zab 协议有两种模式，它们分别是恢复模式和广播模式。

1. 恢复模式

当服务启动或者在领导者崩溃后，Zab 就进入了恢复模式，当领导者被选举出来，且大多数 server 完成了和 leader 的状态同步以后，恢复模式就结束了。状态同步保证了 leader 和 server 具有相同的系统状态。

1. 广播模式

一旦 leader 已经和多数的 follower 进行了状态同步后，它就可以开始广播消息了，即进入广播状态。这时候当一个 server 加入 ZooKeeper 服务中，它会在恢复模式下启动，发现 leader，并和 leader 进行状态同步。待到同步结束，它也参与消息广播。ZooKeeper 服务一直维持在 Broadcast 状态，直到 leader 崩溃了或者 leader 失去了大部分的 followers 支持。

https://thinkwon.blog.csdn.net/article/details/104397719
