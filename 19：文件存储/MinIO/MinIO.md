# 一：简介

官网：https://min.io/

中文文档：http://docs.minio.org.cn/docs/

S3：Simple Storage Service，简单存储服务，这个概念是Amazon在2006年推出的，对象存储就是从那个时候诞生的。S3提供了一个简单Web服务接口，可用于随时在Web上的任何位置存储和检索任何数量的数据。

Object：存储到 Minio 的基本对象，如文件、字节流，Anything...

Bucket：用来存储 Object 的逻辑空间。每个 Bucket 之间的数据是相互隔离的。

Drive：部署 Minio 时设置的磁盘，Minio 中所有的对象数据都会存储在 Drive 里；

Set：一组 Drive 的集合，分布式部署根据集群规模自动划分一个或多个 Set ，每个 Set 中的 Drive 分布在不同位置。一个对象存储在一个 Set 上。

纠删码EC：

纠删码是一种恢复丢失和损坏数据的数学算法，目前，纠删码技术在分布式存储系统中的应用主要有三类，阵列纠删码（Array Code: RAID5、RAID6等）、RS(Reed-Solomon)里德-所罗门类纠删码和LDPC(LowDensity Parity Check Code)低密度奇偶校验纠删码。Erasure Code是一种编码技术，它可以将n份原始数据，增加m份校验数据，并能通过n+m份中的任意n份原始数据，还原为原始数据。即如果有任意小于等于m份的校验数据失效，仍然能通过剩下的数据还原出来。

Minio采用Reed-Solomon code将对象拆分成N/2数据和N/2 奇偶校验块。

在同一集群内，MinIO 自己会自动生成若干纠删组（Set），用于分布存放桶数据。一个纠删组中的一定数量的磁盘发生的故障（故障磁盘的数量小于等于校验盘的数量），通过纠删码校验算法可以恢复出正确的数据。

# 二：部署

单机

```shell
wget -q http://dl.minio.org.cn/server/release/linux-amd64/minio
chmod +x minio
## 启动minio server服务， 执行存储目录/mnt/data
.minio server /mnt/data
```

纠删码模式：采用Reed-Solomon Code将对象拆分成N/2数据和N/2的就校验块； 

集群：



客户端：



mc admin：



# 三：SDK



