ShardingSphere-Jdbc定位为轻量级Java框架，在Java的Jdbc层提供的额外服务。它使用客户端直连数据库，以jar包形式提供服务，可理解为增强版的Jdbc驱动，完全兼容Jdbc和各种ORM框架

# 使用

1：读写分离

引入Jar包依赖

配置文件：配置主从库的数据源

```yml
spring:
    main:
    	allow-bean-definition-overriding : true
    shardingsphere:
    #参数配置，显示sql
        props:
        	sql:
        	show: true
        #配置数据源
        datasource:
            #给每个数据源取别名，下面的dsl ,ds2 , ds3任意取名字
            names: ds1, ds2,ds3
            #给master-dsl每个数据源配置数据库连接信息
            ds1:
                #配置druid数据源
                type: com.alibaba.druid.pool.DruidDatasource
                driver-class-name:com.mysql.cj.jdbc.Driver
                url:
                password: 
                maxPoolsize: 100
                minPoolsize: 5
            #配置ds2-slave
            ds2:
                #配置druid数据源
                type: com.alibaba.druid.pool.DruidDatasource
                driver-class-name:com.mysql.cj.jdbc.Driver
                url:
                password: 
                maxPoolsize: 100
                minPoolsize: 5
             ds3:
                #配置druid数据源
                type: com.alibaba.druid.pool.DruidDatasource
                driver-class-name:com.mysql.cj.jdbc.Driver
                url:
                password: 
                maxPoolsize: 100
                minPoolsize: 5
            #配置默认数据源ds1
            sharding:
                #默认数据源，主要用于写，注意一定要配置读写分离，注意:如果不配置，那么就会把三个节点都当做从slave节点
                default-data-source-name: ds1
                #配置数据源的读写分离，但是数据库一定要做主从复制
                masterslave:
                #配置主从名称，可以任意取名字
                name: ms
                #配置主库master，负责数据的写入
                master-data-source-name : ds1
                #配置从库slave节点
                slave-data-source-names: ds2,ds3
                #配置slave节点的负载均衡均衡策略，采用轮询机制
                load-balance-algorithm-type:round_robin
```

2：分库分表

根据配置的分片策略改写数据SQL；











主键配置规则：



分表的主键策略支持两种：雪花算法和UUID

```yml
#配置默认数据源ds1
sharding:
    #默认数据源，主要用于写，注意一定要配置读写分离，注意:如果不配置，那么就会把三个节点都当做从slave节点
    default-data-source-name: ds1
   #配置分表的规则
   tables:
        #ksd user逻辑表名
        ksd user:
            key-generator:
            #主键的列名
            column : userid
            type: SNOWFLAKE
        #数据节点:数据源$->{0..N）}.逻辑表名$->{0..N}
        actual-data-nodes : ds$->{0..1 } .ksd_user$->{0..1}
        #拆分库策略，也就是什么样子的数据放入放到哪个数据库中
        database-strategy :
            standard:
            	shardingColumn : birthday
            	preciseAlgorithmClassName: com.xuexiangban.shardingjdbc.config.BirthdayAlgorithm
        table-strategy :
            inline:
                sharding-column : age #分片字段(分片键）
                algorithm-expression: ksd_user$-> {age % 2} #分片算法表达式

```





分页查询

 全查，内存分页；性能还是有影响的；





统计count：每一个分库分表全查一遍然后再相加；





分布式事务

跨库：两阶段提交





基础规范：

```txt
表必须有主键，建议使用整型作为主键，占用空间小
禁止使用外键，影响性能，建议业务逻辑上进行关联关系
建议将大字段，访问频率较低的字段和较高的字段进行水平拆分到不同的表;
不要设置添加null,没有设置默认值或空串，null会导致索引失效;
```

