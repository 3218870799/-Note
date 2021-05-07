# 数据可视化

将数据以更加直观的形式展现，文不如表，表不如图

 报表类：Excel，水晶报表

商业智能：Microsoft BI 和 Power-BI

编码类：Echarts.js和D3.js

# 基本使用

底层依赖矢量图形库ZRender，

有丰富的可视化类型，

支持多种数据格式：key-value

支持流数据的支持：



## 使用

1：引入echarts.js文件

```js
<script src="lib/echarts.min.js"></script>
```

2∶准备一个呈现图表的盒子

```html
<div id="main" style="width: 600px;height:400px;"></div>
```

3：初始化echarts实例对象

```js
var myChart = echarts.init(document.getElementById('main'));
```

4︰准备配置项

```js
 var option = {
            title: {
                text: 'ECharts 入门示例'
            },
            tooltip: {},
            legend: {
                data:['销量']
            },
            xAxis: {
                data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
            },
            yAxis: {},
            series: [{
                name: '销量',
                type: 'bar',
                data: [5, 20, 36, 10, 10, 20]
            }]
        };
```

5：将配置项设置给Echarts实例对象

```js
myChart.setOption(option);
```

不同的只有配置项不同

详见：https://echarts.apache.org/zh/option.html#title官方文档配置项

系列类型（`series.type`）至少有：[line](https://echarts.apache.org/zh/option.html#series-line)（折线图）、[bar](https://echarts.apache.org/zh/option.html#series-bar)（柱状图）、[pie](https://echarts.apache.org/zh/option.html#series-pie)（饼图）、[scatter](https://echarts.apache.org/zh/option.html#series-scatter)（散点图）、[graph](https://echarts.apache.org/zh/option.html#series-graph)（关系图）、[tree](https://echarts.apache.org/zh/option.html#series-tree)（树图）、...

## 常用图表

### 柱状图：

最大值，最小值，平均值，

数值显示，

### 折线图

平滑风格smooth，lineStyle

面积：

### 地图

