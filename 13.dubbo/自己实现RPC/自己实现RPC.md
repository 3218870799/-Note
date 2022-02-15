# 一：简介

RPC远程过程调用，及像调用本地方法一样调用远程服务；目前外界使用较多的有gRPC、Dubbo、Spring Cloud等；

三要素：

- 服务提供方 Serivce Provider
- 服务消费方 Servce Consumer
- 注册中心 Registery



# 二：技术选型

1：注册中心

目前成熟的注册中心有Zookeeper，Nacos，Consul，Eureka，它们的主要比较如下：



![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJo2LWTB4hyicxX0AhG2PVgCicGRG4DibNbhZdibyh6NQ2BnZdwG1FKBIUNOnUXpd4CPlt9Y8E0WFlCvQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



2：IO通信框架

Netty是一个高性能事件驱动型的非阻塞的IO(NIO)框架。

3：通信协议

TCP通信过程中会根据TCP缓冲区的实际情况进行包的划分，所以在业务上认为一个完整的包可能会被TCP拆分成多个包进行发送，也有可能把多个小的包封装成一个大的数据包发送，这就是所谓的TCP粘包和拆包问题。所以需要对发送的数据包封装到一种通信协议里。



业界的主流协议的解决方案可以归纳如下：



1. 消息定长，例如每个报文的大小为固定长度100字节，如果不够用空格补足。
2. 在包尾特殊结束符进行分割。
3. 将消息分为消息头和消息体，消息头中包含表示消息总长度（或者消息体长度）的字段。



很明显1，2都有些局限性，本实现采用方案3，



4：序列化协议

选用Protobuf，其序列化后码流小性能高，非常适合RPC调用，Google自家的gRPC也是用其作为通信协议。

5：负载均衡

主要负载均衡策略，随机和轮询，带权重的随机和轮询；



# 三：整体架构

![图片](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJo2LWTB4hyicxX0AhG2PVgCicibRVIOMkKEbXlnIWibBia2kwCrwZLsjscH4BcqgdHxdC3alFV4xKshibw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

# 四：实现

https://mp.weixin.qq.com/s/B9NvD1mfAJz_sPWH9aug8A







