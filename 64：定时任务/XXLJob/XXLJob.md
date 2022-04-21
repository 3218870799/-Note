XXL-JOB[3] 是一个开箱即用的轻量级分布式任务调度系统，其核心设计目标是开发迅速、学习简单、轻量级、易扩展，在开源社区广泛流行。



XXL-JOB 是 Master-Slave 架构，Master 负责任务的调度，Slave 负责任务的执行，架构图如下：



![图片](https://mmbiz.qpic.cn/mmbiz_png/yvBJb5IiafvlD6cEia1o9Lx6UTF7TUNUBe7G9wkqvB4BsNzOicuJtt5ZtGpRr3ou2tXicJia9hwyUna8keBgygE1V0A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



XXL-JOB 接入也很方便，不同于 ElasticJob 定义任务实现类，是通过@XxlJob 注解定义 JobHandler。



```
@Component
public class SampleXxlJob {
    private static Logger logger = LoggerFactory.getLogger(SampleXxlJob.class);


    /**
     * 1、简单任务示例（Bean模式）
     */
    @XxlJob("demoJobHandler")
    public ReturnT<String> demoJobHandler(String param) throws Exception {
        XxlJobLogger.log("XXL-JOB, Hello World.");

        for (int i = 0; i < 5; i++) {
            XxlJobLogger.log("beat at:" + i);
            TimeUnit.SECONDS.sleep(2);
        }
        return ReturnT.SUCCESS;
    }


    /**
     * 2、分片广播任务
     */
    @XxlJob("shardingJobHandler")
    public ReturnT<String> shardingJobHandler(String param) throws Exception {

        // 分片参数
        ShardingUtil.ShardingVO shardingVO = ShardingUtil.getShardingVo();
        XxlJobLogger.log("分片参数：当前分片序号 = {}, 总分片数 = {}", shardingVO.getIndex(), shardingVO.getTotal());

        // 业务逻辑
        for (int i = 0; i < shardingVO.getTotal(); i++) {
            if (i == shardingVO.getIndex()) {
                XxlJobLogger.log("第 {} 片, 命中分片开始处理", i);
            } else {
                XxlJobLogger.log("第 {} 片, 忽略", i);
            }
        }

        return ReturnT.SUCCESS;
    }
}
```



XXL-JOB 相较于 ElasticJob，最大的特点就是功能比较丰富，可运维能力比较强，不但支持控制台动态创建任务，还有调度日志、运行报表等功能。



![图片](https://mmbiz.qpic.cn/mmbiz_png/yvBJb5IiafvlD6cEia1o9Lx6UTF7TUNUBe9MogbLd0UFu749BHibHOatCb4Lu6cdia6oMnMwPwk7a5AGckHQ2unPYA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



![图片](https://mmbiz.qpic.cn/mmbiz_png/yvBJb5IiafvlD6cEia1o9Lx6UTF7TUNUBeF65ib9eicAiaEkf6j1PjxwwlQ2iajQgmt5BtKeF390IuH8RRoyhd2Rpksg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



XXL-JOB 的历史记录、运行报表和调度日志，都是基于数据库实现的：



![图片](https://mmbiz.qpic.cn/mmbiz_png/yvBJb5IiafvlD6cEia1o9Lx6UTF7TUNUBeafEwicGrFsf4QQ81JWBxIHY8e7pQBdnjX4vVcicRySUnvvkRJRgzefibQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



由此可以看出，XXL-JOB 所有功能都依赖数据库，且调度中心不支持分布式架构，在任务量和调度量比较大的情况下，会有性能瓶颈。不过如果对任务量级、高可用、监控报警、可视化等没有过高要求的话，XXL-JOB 基本可以满足定时任务的需求。