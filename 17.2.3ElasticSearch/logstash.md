Logstash是一个开源的服务器端数据处理管道，能够同时从多个来源采集数据，转换数据，然后将数据发送到最喜欢的存储库中（我们的存储库当然是ElasticSearch）

ElasticStack的架构图，可以看到Logstash是充当数据处理的需求的，当我们的数据需要处理的时候，会将它发送到Logstash进行处理，否则直接送到ElasticSearch中

Logstash可以处理各种各样的输入，从文档，图表中=，数据库中，然后处理完后，发送到

Logstash主要是将数据源的数据进行一行一行的处理，同时还直接过滤切割等功能。

首先到官网下载logstash：https://www.elastic.co/cn/downloads/logstash

下载完成后，使用xftp工具，将其丢入到服务器中

```bash
#检查jdk环境，要求jdk1.8+
java -version

#解压安装包
tar -xvf logstash-7.9.1.tar.gz

#第一个logstash示例
bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

其实原来的logstash的作用，就是为了做数据的采集，但是因为logstash的速度比较慢，所以后面使用beats来代替了Logstash，当我们使用上面的命令进行启动的时候，就可以发现了，因为logstash使用java写的，首先需要启动虚拟机，最后下图就是启动完成的截图