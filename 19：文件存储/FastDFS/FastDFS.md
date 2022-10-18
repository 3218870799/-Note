# 一：简介

FastDFS(Fast Distributed file system)用`c语言`编写的一款开源的轻量级分布式文件系统

文件存储,文件访问(文件上传,文件下载)文件同步等,解决了大容量存储和负载均衡的问题

## 组成

FastDFS架构由`Client`,`Tracker server`和`Storage server`组成

FastDFS文件系统由两大部分构成，一个是客户端，一个是服务端

客户端通常指我们的程序，比如我们的Java程序去连接FastDFS、操作FastDFS，那我们的Java程序就是一个客户端，FastDFS提供专有API访问，目前提供了C、Java和PHP几种编程语言的API，用来访问FastDFS文件系统。

服务端由两个部分构成：一个是跟踪器（tracker），一个是存储节点（storage）

跟踪器（tracker）主要做调度工作，在内存中记录集群中存储节点storage的状态信息，是前端Client和后端存储节点storage的枢纽。因为相关信息全部在内存中，Tracker server的性能非常高，一个较大的集群（比如上百个group）中有3台就足够了。

存储节点（storage）用于存储文件，包括文件和文件属性（meta data）都保存到存储服务器磁盘上，完成文件管理的所有功能：文件存储、文件同步和提供文件访问等。



生成的文件目录结构

![image-20220913111157507](media/image-20220913111157507.png)





# 二：环境搭建

1：提前安装gcc ，libevent,libevent-devel;

```shell
yum install gcc libevent libevent-devel -y
```

2：安装libfastcommon库，是 FastDFS 文件系统运行需要的公共 C 语言函数库；

下载地址：https://github.com/happyfish100

下载并解压：

# 三：使用

1：文件上传方法

```java
public static void fileUpload(){
    try {
        //1. 获取StorageClient对象
        StorageClient storageClient = getStorageClient();
        //2.上传文件  第一个参数：本地文件路径 第二个参数：上传文件的后缀 第三个参数：文件信息
        String [] uploadArray = storageClient.upload_file("D:/aa.txt","txt",null);
        for (String str:uploadArray) {
            System.out.println(str);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } catch (MyException e) {
        e.printStackTrace();
    } finally {
        closeFastDFS();
    }
}
public static StorageClient getStorageClient() throws IOException, MyException {
    //1.加载配置文件，默认去classpath下加载
    ClientGlobal.init("fdfs_client.conf");
    //2.创建TrackerClient对象
    TrackerClient trackerClient = new TrackerClient();
    //3.创建TrackerServer对象
    trackerServer = trackerClient.getConnection();
    //4.创建StorageServler对象
    storageServer = trackerClient.getStoreStorage(trackerServer);
    //5.创建StorageClient对象，这个对象完成对文件的操作
    StorageClient storageClient = new StorageClient(trackerServer,storageServer);
    return storageClient;
}
public static void closeFastDFS() {
    if (storageServer != null) {
        try {
            storageServer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    if (trackerServer != null) {
        try {
            trackerServer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

2：文件下载方法

```java
//下载文件的方法
public static void fileDownload(){
    try {
        //1. 获取StorageClient对象
        StorageClient storageClient = getStorageClient();
        //2.下载文件 返回0表示成功，其它均表示失败
        int num = storageClient.download_file("group1",
                "M00/00/00/wKjrgFxOqueAAPWKAAAAKAM14xY563.txt","E:/bb.txt");
        System.out.println(num);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (MyException e) {
        e.printStackTrace();
    } finally {
        closeFastDFS();
    }
}

```

3：删除文件

```java
//删除文件的方法
public static void fileDelete(){
    try {
        //1. 获取StorageClient对象
        StorageClient storageClient = getStorageClient();
        //2.删除文件 返回0表示成功，其它均表示失败
        int num = storageClient.delete_file("group1",
                "M00/00/00/wKjrgFxOqueAAPWKAAAAKAM14xY563.txt");
        System.out.println(num);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (MyException e) {
        e.printStackTrace();
    } finally {
        closeFastDFS();
    }
}

```

# 四：集群

