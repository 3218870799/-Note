ElasticJob[2] 是一款基于 Quartz 开发，依赖 Zookeeper 作为注册中心、轻量级、无中心化的分布式任务调度框架，目前已经通过 Apache 开源。



ElasticJob 相对于 Quartz 来说，从功能上最大的区别就是支持分片，可以将一个任务分片参数分发给不同的机器执行。架构上最大的区别就是使用 Zookeeper 作为注册中心，不同的任务分配给不同的节点调度，不需要抢锁触发，性能上比 Quartz 上强大很多，架构图如下：



![图片](https://mmbiz.qpic.cn/mmbiz_png/yvBJb5IiafvlD6cEia1o9Lx6UTF7TUNUBepXCyFTJwBHG7VFdibIGplF9MccJuOVS3dwVqkKXBnB09DPPJ1HFY8fA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



开发上也比较简单，和 springboot 结合比较好，可以在配置文件定义任务如下：



```
elasticjob:
  regCenter:
    serverLists: localhost:2181
    namespace: elasticjob-lite-springboot
  jobs:
    simpleJob:
      elasticJobClass: org.apache.shardingsphere.elasticjob.lite.example.job.SpringBootSimpleJob
      cron: 0/5 * * * * ?
      timeZone: GMT+08:00
      shardingTotalCount: 3
      shardingItemParameters: 0=Beijing,1=Shanghai,2=Guangzhou
    scriptJob:
      elasticJobType: SCRIPT
      cron: 0/10 * * * * ?
      shardingTotalCount: 3
      props:
        script.command.line: "echo SCRIPT Job: "
    manualScriptJob:
      elasticJobType: SCRIPT
      jobBootstrapBeanName: manualScriptJobBean
      shardingTotalCount: 9
      props:
        script.command.line: "echo Manual SCRIPT Job: "
```



实现任务接口如下：



```
@Component
public class SpringBootShardingJob implements SimpleJob {

    @Override
    public void execute(ShardingContext context) {
        System.out.println("分片总数="+context.getShardingTotalCount() + ", 分片号="+context.getShardingItem()
            + ", 分片参数="+context.getShardingParameter());
    }

}
```



运行结果如下：



```
分片总数=3, 分片号=0, 分片参数=Beijing
分片总数=3, 分片号=1, 分片参数=Shanghai
分片总数=3, 分片号=2, 分片参数=Guangzhou
```



同时，ElasticJob 还提供了一个简单的 UI，可以查看任务的列表，同时支持修改、触发、停止、生效、失效操作。



![图片](https://mmbiz.qpic.cn/mmbiz_png/yvBJb5IiafvlD6cEia1o9Lx6UTF7TUNUBeQNgta4xXViaggBxZIXr1zP65oRq8fNS2jAlAVRic6p9fn7VRl9fAeuDg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



遗憾的是，ElasticJob 暂不支持动态创建任务。