Beats主要用于采集数据，

Beats平台其实是一个轻量性数据采集器，通过集合多种单一用途的采集器，从成百上千台机器中向Logstash或ElasticSearch中发送数据。

通过Beats包含以下的数据采集功能

- Filebeat：采集日志文件
- Metricbeat：采集指标
- Packetbeat：采集网络数据

如果我们的数据不需要任何处理，那么就可以直接发送到ElasticSearch中

如果们的数据需要经过一些处理的话，那么就可以发送到Logstash中，然后处理完成后，在发送到ElasticSearch

最后在通过Kibana对我们的数据进行一系列的可视化展示



# 二：Filebeat

Filebeat是一个轻量级的日志采集器

当你面对成百上千、甚至成千上万的服务器、虚拟机和容器生成的日志时，请告别SSH吧！Filebeat将为你提供一种轻量型方法，用于转发和汇总日志与文件，让简单的事情不再繁杂，关于Filebeat的记住以下两点：

- 轻量级日志采集器
- 输送至ElasticSearch或者Logstash，在Kibana中实现可视化

官网地址：https://www.elastic.co/cn/downloads/beats/filebeat

### Filebeat工作原理

Filebeat主要由下面几个组件组成： harvester、prospector 、input

#### harvester

- 负责读取单个文件的内容
- harvester逐行读取每个文件（一行一行读取），并把这些内容发送到输出
- 每个文件启动一个harvester，并且harvester负责打开和关闭这些文件，这就意味着harvester运行时文件描述符保持着打开的状态。
- 在harvester正在读取文件内容的时候，文件被删除或者重命名了，那么Filebeat就会续读这个文件，这就会造成一个问题，就是只要负责这个文件的harvester没用关闭，那么磁盘空间就不会被释放，默认情况下，Filebeat保存问价你打开直到close_inactive到达

#### prospector

- prospector负责管理harvester并找到所有要读取的文件来源
- 如果输入类型为日志，则查找器将查找路径匹配的所有文件，并为每个文件启动一个harvester
- Filebeat目前支持两种prospector类型：log和stdin
- Filebeat如何保持文件的状态
  - Filebeat保存每个文件的状态并经常将状态刷新到磁盘上的注册文件中
  - 该状态用于记住harvester正在读取的最后偏移量，并确保发送所有日志行。
  - 如果输出（例如ElasticSearch或Logstash）无法访问，Filebeat会跟踪最后发送的行，并在输出再次可以用时继续读取文件。
  - 在Filebeat运行时，每个prospector内存中也会保存的文件状态信息，当重新启动Filebat时，将使用注册文件的数量来重建文件状态，Filebeat将每个harvester在从保存的最后偏移量继续读取
  - 文件状态记录在data/registry文件中

### input

- 一个input负责管理harvester，并找到所有要读取的源
- 如果input类型是log，则input查找驱动器上与已定义的glob路径匹配的所有文件，并为每个文件启动一个harvester
- 每个input都在自己的Go例程中运行

### Module

前面要想实现日志数据的读取以及处理都是自己手动配置的，其实，在Filebeat中，有大量的Module，可以简化我们的配置，直接就可以使用，内置了很多的module，但是都没有启用，如果需要启用需要进行enable操作：

# 三：Metricbeat

- 定期收集操作系统或应用服务的指标数据
- 存储到Elasticsearch中，进行实时分析

### Metricbeat组成

Metricbeat有2部分组成，一部分是Module，另一个部分为Metricset

- Module
  - 收集的对象：如 MySQL、Redis、Nginx、操作系统等
- Metricset
  - 收集指标的集合：如 cpu、memory，network等

首先我们到[官网](https://www.elastic.co/cn/downloads/beats/metricbeat)，找到Metricbeat进行下载