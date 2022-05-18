固定流程直接搞个表也能做，但是如果加个流程，多部门协同审批这种，代码变化就比较大了；

# BPMN建模语言

业务流程模型和符号；



# 搭建

1：

IDEA安装插件：actiBPM；新版本的安不上，用Activiti BPMN visualizer代替；

2：初始化数据库

创建一个单独的数据库；

配置文件activiti.cfg.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                    http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/contex
http://www.springframework.org/schema/context/spring-context.xsd
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- 这里可以使用 链接池 dbcp-->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/activiti?serverTimezone=GMT%2B8" />
        <property name="username" value="root" />
        <property name="password" value="oplus@2016" />
        <property name="maxActive" value="3" />
        <property name="maxIdle" value="1" />
    </bean>

    <bean id="processEngineConfiguration"
          class="org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration">
        <!-- 引用数据源 上面已经设置好了-->
        <property name="dataSource" ref="dataSource" />
        <!--activiti数据库表处理策略，如果没有表则创建-->
        <property name="databaseSchemaUpdate" value="true"></property>
    </bean>
</beans>
```

执行样例，创建25张表差不多；

```java
ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
```



act_hi开头的：历史相关的；

act_ru开头的：运行时相关的；

act_ge开头的：通用的信息；



3：画图BPMN文件



4：部署实例

```java
    /**
     * 部署流程定义  文件上传方式
     */
    @Test
    public void testDeployment(){
//        1、创建ProcessEngine
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
//        2、得到RepositoryService实例
        RepositoryService repositoryService = processEngine.getRepositoryService();
//        3、使用RepositoryService进行部署
        Deployment deployment = repositoryService.createDeployment()
                .addClasspathResource("bpmn/Leave.bpmn") // 添加bpmn资源
                //png资源命名是有规范的。Leave.myLeave.png|jpg|gif|svg  或者Leave.png|jpg|gif|svg
                .addClasspathResource("bpmn/Leave.myLeave.png")  // 添加png资源
                .name("请假申请流程")
                .deploy();
//        4、输出部署信息
        System.out.println("流程部署id：" + deployment.getId());
        System.out.println("流程部署名称：" + deployment.getName());
    }
```

分析表操作：

act_re_deployment：记录了一个部署

act_re_procdef：流程定义；

部署方式二：压缩包上传bpmn文件和图片进行解析部署；



5：开启一个流程

```java
    /**
     * 启动流程实例
     */
    @Test
    public void testStartProcess(){
//        1、创建ProcessEngine
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
//        2、获取RunTimeService
        RuntimeService runtimeService = processEngine.getRuntimeService();
//        3、根据流程定义Id启动流程
        ProcessInstance processInstance = runtimeService
                .startProcessInstanceByKey("myLeave");
//        输出内容
        System.out.println("流程定义id：" + processInstance.getProcessDefinitionId());
        System.out.println("流程实例id：" + processInstance.getId());
        System.out.println("当前活动Id：" + processInstance.getActivityId());
    }
```

6：流程中的人查询自己的待处理流程

```java
    /**
     * 查询当前个人待执行的任务
     */
    @Test
    public void testFindPersonalTaskList() {
//        任务负责人
        String assignee = "worker";
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
//        创建TaskService
        TaskService taskService = processEngine.getTaskService();
//        根据流程key 和 任务负责人 查询任务
        List<Task> list = taskService.createTaskQuery()
                .processDefinitionKey("myLeave") //流程Key
                .taskAssignee(assignee)//只查询该任务负责人的任务
                .list();

        for (Task task : list) {
            System.out.println("流程实例id：" + task.getProcessInstanceId());
            System.out.println("任务id：" + task.getId());
            System.out.println("任务负责人：" + task.getAssignee());
            System.out.println("任务名称：" + task.getName());

        }
    }
```

7：流程中的人执行自己的任务

```java
    // 完成任务
    @Test
    public void completTask(){
//        获取引擎
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
//        获取taskService
        TaskService taskService = processEngine.getTaskService();

//        根据流程key 和 任务的负责人 查询任务
//        返回一个任务对象
        Task task = taskService.createTaskQuery()
                .processDefinitionKey("myLeave") //流程Key
                .taskAssignee("worker")  //要查询的负责人
                .singleResult();

//        完成任务,参数：任务id
        taskService.complete(task.getId());
    }
```

# 概念

流程定义ProcessDefinition和流程实例ProcessInstance，类似于Java中类和对象的概念；

流程定义ProcessDefinition是以BPMN文件定义的一个工作流程，是一组规范；员工发起一次请假，就会实例化一个请假流程实例；

BusinessKey：业务关键字；

数据库长度255；



## 挂起，激活流程

已经挂起的任务不能继续执行；

全部流程实例的 挂起和 激活

```java
 /**
     * 全部流程实例的 挂起和 激活
     * suspend 暂停
     */
    @Test
    public void suspendAllProcessInstance(){
//        1、获取流程引擎
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
//        2、获取Repositoryservice
        RepositoryService repositoryService = processEngine.getRepositoryService();
//        3、查询流程定义,获取流程定义的查询对象
        ProcessDefinition processDefinition = repositoryService.createProcessDefinitionQuery()
                .processDefinitionKey("myLeave")
                .singleResult();
//        4、获取当前流程定义的实例是否都是挂起状态
        boolean suspended = processDefinition.isSuspended();
//        5、获取流程定义的id
        String definitionId = processDefinition.getId();
//        6、如果是挂起状态，改为激活状态
        if(suspended){
//            如果是挂起，可以执行激活的操作,参数1：流程定义id 参数2：是否激活，参数3：激活时间
            repositoryService.activateProcessDefinitionById(definitionId,
                    true,
                    null);
            System.out.println("流程定义id:"+definitionId+"，已激活");
        }else {
//        7、如果是激活状态，改为挂起状态,参数1：流程定义id 参数2：是否暂停 参数3 ：暂停的时间
            repositoryService.suspendProcessDefinitionById(definitionId,
                    true,
                    null);
            System.out.println("流程定义id:"+definitionId+"，已挂起");
        }
    }
```

挂起、激活单个流程实例

```java
    /**
     * 挂起、激活单个流程实例
     */
    @Test
    public void suspendSingleProcessInstance(){
//        1、获取流程引擎
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
//        2、RuntimeService
        RuntimeService runtimeService = processEngine.getRuntimeService();
//        3、通过RuntimeService获取流程实例对象
        ProcessInstance instance = runtimeService.createProcessInstanceQuery()
                .processInstanceId("7501")
                .singleResult();
//        4、得到当前流程实例的暂停状态,true-已暂停  false -激活
        boolean suspended = instance.isSuspended();
//        5、获取流程实例id
        String instanceId = instance.getId();
//        6、判断是否已经暂停，如果已经暂停，就执行激活操作
        if(suspended){
//            如果已经暂停，就执行激活
            runtimeService.activateProcessInstanceById(instanceId);
            System.out.println("流程实例id:"+instanceId+"已经激活");
        }else {
//        7、如果是激活状态，就执行暂停操作
            runtimeService.suspendProcessInstanceById(instanceId);
            System.out.println("流程实例id:"+instanceId+"已经暂停");
        }
    }
```

## 流量变量

比如：如果请假超过3天需要总经理审批；

启动时设置：

```xlm



```

