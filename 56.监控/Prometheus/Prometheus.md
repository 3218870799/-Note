# 一：简介

`Prometheus`是一个开源的系统监控和报警系统，现在已经加入到CNCF基金会，成为继k8s之后第二个在CNCF托管的项目，在kubernetes容器管理系统中，通常会搭配prometheus进行监控，同时也支持多种`exporter`采集数据，还支持`pushgateway`进行数据上报，Prometheus性能足够支撑上万台规模的集群。监控时可以使用Grafana就行可视化；同样可以监控云上系统；=

样本：在时间序列中的每一个点称为一个样本（sample），样本由以下三部分组成：

- 指标（`metric`）：指标名称和描述当前样本特征的 labelsets；
- 时间戳（`timestamp`）：一个精确到毫秒的时间戳；
- 样本值（`value`）： 一个 folat64 的浮点型数据表示当前样本的值。

**表示方式**：通过如下表达方式表示指定指标名称和指定标签集合的时间序列：`<metric name>{<label name>=<label value>, ...}`

例如，指标名称为 `api_http_requests_total`，标签为 `method="POST"` 和 `handler="/messages"` 的时间序列可以表示为：`api_http_requests_total{method="POST", handler="/messages"}`

它是基于时间序列数据进行记录的：

特点：

多维度的数据模型；



原理架构图：



