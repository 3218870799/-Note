Kibana 是一款开源的数据分析和可视化平台，它是 Elastic Stack 成员之一，设计用于和 Elasticsearch 协作。您可以使用 Kibana 对 Elasticsearch 索引中的数据进行搜索、查看、交互操作。您可以很方便的利用图表、表格及地图对数据进行多元化的分析和呈现。

到下载地址，选择对应的版本：https://www.elastic.co/cn/downloads/kibana

下载完成后，将文件拷贝到我们的服务器上，然后解压

```bash
# 解压
tar -zxvf kibana-7.9.1-linux-x86_64.tar.gz

# 重命名
mv kibana-7.9.1-linux-x86_64 kibana
```

然后在进入kibana目录，找到config文件夹下的kibana.yml进行配置的修改

```bash
vim /soft/kibana/config/kibana.yml
```

然后找到下面的内容

```bash
#对外暴露服务的地址
server.host: "0.0.0.0" 

#配置Elasticsearch
elasticsearch.url: "http://127.0.0.1:9200" 
```

启动

修改配置完成后，我们就可以启动kibana了

```bash
#启动
./bin/kibana
```

点击启动，发现报错了

![image-20200924195011533](http://image.moguit.cn/a858839fe74d44d1a84c6c8a03211f8a)

原因是kibana不能使用root用户进行启动，所以我们切换到elsearch用户

```bash
# 将soft文件夹的所属者改成elsearch
chown elsearch:elsearch /soft/ -R

# 切换用户
su elsearch

# 启动
./bin/kibana
```

然后打开下面的地址，即可访问我们的kibana了

```bash
http://202.193.56.222:5601/
```

## 功能

- Discover：数据探索
- Visualize：可视化
- Dashboard：仪表盘
- Timelion：时序控件
- Canvas：画布
- Machine Learning：机器学习
- Infrastructure：基本信息
- Logs：数据日志展示
- APM：性能监控
- Dev Tools：开发者工具
- Monitoring：监控
- Management：管理

### Metricbeat仪表盘

现在将Metricbeat的数据展示在Kibana中，首先需要修改我们的MetricBeat配置

```bash
#修改metricbeat配置
setup.kibana:
  host: "192.168.40.133:5601"
  
#安装仪表盘到Kibana【需要确保Kibana在正常运行，这个过程可能会有些耗时】
./metricbeat setup --dashboards
```

安装完成后，如下所示

![image-20200924203831606](http://image.moguit.cn/baebc3abfb4344fe959944ac58b7bd3f)

然后我们启动Metricbeat

```
./metricbeat -e
```

然后到kibana页面下，找到我们刚刚安装的仪表盘

![image-20200924204708099](http://image.moguit.cn/1d3047e1ce4f43b69c1b3d29e4b4d7cf)

然后我们就能够看到非常多的指标数据了