百度开发的：

快速上手

官方文档：https://www.highcharts.com.cn/docs/start-helloworld

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>第一个 Highcharts 图表</title>
</head>
<body>
    <!-- 图表容器 DOM -->
    <div id="container" style="width: 600px;height:400px;"></div>
    <!-- 引入 highcharts.js -->
    <script src="http://cdn.highcharts.com.cn/highcharts/highcharts.js"></script>
    <script>
        // 图表配置
        var options = {
            chart: {
                type: 'bar'                          //指定图表的类型，默认是折线图（line）
            },
            title: {
                text: '我的第一个图表'                 // 标题
            },
            xAxis: {
                categories: ['苹果', '香蕉', '橙子']   // x 轴分类
            },
            yAxis: {
                title: {
                    text: '吃水果个数'                // y 轴标题
                }
            },
            series: [{                              // 数据列
                name: '小明',                        // 数据列名
                data: [1, 0, 4]                     // 数据
            }, {
                name: '小红',
                data: [5, 7, 3]
            }]
        };
        // 图表初始化函数
        var chart = Highcharts.chart('container', options);
    </script>
</body>
</html>
```

堆积图

https://www.highcharts.com.cn/demo/highcharts/area-stacked

备份

```thnl
    
    var chart = Highcharts.chart('container', {
        chart: {
            type: 'area'
        },
        title: {
            text: '规模'
        },
        xAxis: {
            categories: result.xData,
            tickmarkPlacement: 'on',
            title: {
                enabled: false
            }
        },
        yAxis: {
            title: {
                text: '元'
            },
            labels: {
                formatter: function () {
                    return this.value / 1000;
                }
            }
        },
        tooltip: {
            split: true,
            valueSuffix: ' 百万'
        },
        plotOptions: {
            area: {
                stacking: 'normal',
                lineColor: '#666666',
                lineWidth: 1,
                marker: {
                    lineWidth: 1,
                    lineColor: '#666666'
                }
            }
        },
        series: result.yData
    });
```

动态更改图表的信息：

```javascript
<!--定义全局的图表，拿到图表对象-->
var chart = Highcharts.chart('container',option);
<!--使用update方法,动态加载图表内容-->
    chart.update({
        chart: {
            inverted: false,
            polar: false
        },
        subtitle: {
            text: '普通的'
        }
    });
```

