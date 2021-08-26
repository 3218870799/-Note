当Quartz要注入Spring Bean对象时，会报错，因为Job对象不是Spring进行管理的，当job对象中注入Spring Bean会产生空指针异常 即无法注入

解决办法:

采用工厂模式，写一个JobFactory ，在ApplicationContext 中配置jobFactory，由JobFactory生成job时候，自动注入Spring Bean对象

```java
@Service("jobFactory")
public class JobFactory extends AdaptableJobFactory {
      @Autowired
     private AutowireCapableBeanFactory capableBeanFactory;
      @Override
      protected Object createJobInstance(TriggerFiredBundle bundle)
             throws Exception {
         Object jobInstance = super.createJobInstance(bundle);
         capableBeanFactory.autowireBean(jobInstance);
         return jobInstance;
     }
 }
```

然后在spring配置文件中配置

```xml
    <bean id="myJobFactory" class="com.xqc.XXX.jobs.common.CustomJobFactory"></bean>

    <!-- 　　　　<bean id="executor" class="org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor">
            线s程池维护线程的最少数量
            <property name="corePoolSize" value="22" />
            允许的空闲时间
            <property name="keepAliveSeconds" value="1000" />
            线程池维护线程的最大数量
            <property name="maxPoolSize" value="44" />
          缓存队列
           <property name="queueCapacity" value="88" />
          对拒绝task的处理策略
         <property name="rejectedExecutionHandler">
               <bean class="java.util.concurrent.ThreadPoolExecutor$CallerRunsPolicy" />
           </property>
       </bean>        -->
    <bean name="quartzScheduler"
          class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="jobFactory" ref="myJobFactory"/>
        <!--               　　　　　　　<property name="quartzProperties"> 
             　　　　<props>
             　　　　主要是这个参数
             　　　　　　<prop key="org.quartz.scheduler.skipUpdateCheck">true</prop> 
             　　　　</props>
             　　</property>　　 -->
        <!--     <property name="taskExecutor" ref="executor" /> -->
        <!-- triggers已放置到数据库中配置 -->
        <!--      <property name="triggers">
                       <list>
                          <ref bean="threadMonitor_CronTrigger"/>
                           <ref bean="tabledepathJob_CronTrigger"/>
                           <ref bean="log2MongoDbCronTrigger"></ref>
                          <ref bean="mq2Db_cfps_CronTrigger"/>
                           <ref bean="mq2Db_dyncgo_CronTrigger"/>
                           <ref bean="mq2Db_dynhgh_CronTrigger"/>
                           <ref bean="mq2Db_dynkwe_CronTrigger"/>
                           <ref bean="mq2Db_dynngb_CronTrigger"/>
                           <ref bean="mq2Db_dynszx_CronTrigger"/>
                           <ref bean="mq2Db_lsc_CronTrigger"/> 
                           <ref bean="mq2Db_lst_CronTrigger"/>
                           <ref bean="mq2DbJob_cfps2cargos_CronTrigger"/>
                           
                         <ref bean="task1Trigger"/>
                       </list>
           </property>　　　　 -->
        <property name="waitForJobsToCompleteOnShutdown" value="true"></property>
        <property name="configLocation" value="classpath:system.properties"></property>
    </bean>
```

 