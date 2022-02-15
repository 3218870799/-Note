## 一：JBPM是什么？有什么用？能解决什么问题？

现实生活中有很多需要走一些流程的过程，比如请假流程，报销流程等，使用工作流框架，即可写一个流程即可，添加流程时不在繁琐的建立新的各种配置。

1：jBPM,全称是Java Business Process Management,是一种基于J2EE的轻量级工作流管理系统。

2：他可以使用 JPdl详细定义状态图的每个部分，如起始，结束状态，状态之间的转换等。

3：JBPM还有一个特点就是他使用 Hibernate 来管理他的数据库，他会在数据库中建立十八张表，项目中的流程都存在于这18张表中。不仅如此，他还提供了各个数据库的执行脚本

![img](https://images2018.cnblogs.com/blog/1174906/201808/1174906-20180809204406117-919356530.png)

## 二：基本操作

### 1：以请假为例说明其基本操作

1：添加Jar包，提供三个核心配置文件

核心配置文件-----jbpm.cfg.xml
hibernate框架配置文件-----jbpm.hibernate.cfg.xml
日志配置文件-----logging.properties

2：加载发布流程定义：公司中的管理员通过jBPM的designer插件设计了一套请假的流程是什么样子的。

.3：对于流程的基本操作

3.1）部署流程

```java
NewDeployment deployment = processEngine.getRepositoryService().createDeployment();
		 ZipInputStream zipInputStream = new ZipInputStream(new FileInputStream(new File("D:\\room\\jbpm\\process\\hello.zip")));
		 deployment.addResourcesFromZipInputStream(zipInputStream);
		 String id = deployment.deploy();
		 System.out.println(id);
```

3.2）删除流程

```java
String deploymentId = "1";
processEngine.getRepositoryService().deleteDeployment(deploymentId);	
```

3.3）查询流程

```java
ProcessDefinitionQuery query = processEngine.getRepositoryService().createProcessDefinitionQuery();
long count = query.count();
System.out.println("当前系统流程定义的数量 = " + count);
List<ProcessDefinition> list = query.list();
```

3.4）获取一次流程对应的文档（xml文件以及png图片）

```java
Set<String> names = processEngine.getRepositoryService().getResourceNames(deploymentId);
for(String name:names){
	System.out.println(name);
}
//获得一次部署对应的文件输入流
String resourceName = "helloworld.jpdl.xml";
//获得部署对应的输入流
InputStream in = processEngine.getRepositoryService().getResourceAsStream(deploymentId, resourceName);
 //通过此输出流将文件保存到本地磁盘
 OutputStream out = new FileOutputStream(new File("d:\\" + resourceName));
 byte[] b = new byte[1024];
 int len = 0;
 while((len = in.read(b)) != -1){
 	out.write(b, 0, len);
 }
 out.close();
 in.close();
```

4 启动流程：现在可以请假了！！

```java
 String processDefinitionId = "请假流程-2";
 ProcessInstance processInstance = processEngine.getExecutionService().startProcessInstanceById(processDefinitionId);
```

5 处理任务：

5.1查询我可以处理的任务

```java
String userId = "王五";
//获取任务查询对象
TaskQuery query = processEngine.getTaskService().createTaskQuery();
query.assignee(userId);//添加过滤条件
List<Task> list = query.list();
```

5.2 办理任务

```java
 String taskId = "50001";
processEngine.getTaskService().completeTask(taskId);
```

6 记录流程的相关状态

### 5：在操作过程中常用的Service

1）processEngine.getRepositoryService()----流程定义的部署，删除，查询，获得流程定义文档

2）processEngine.getExecutionService()-----启动一个流程实例，跳转到下一步

3）processEngine.getTaskService()----------查询我的任务列表，办理任务

### 6：在炒作过程中常用的查询对象

1）流程定义查询对象---processEngine.getRepositoryService().createProcessDefinitionQuery();

2）流程实例查询对象---processEngine.getExecutionService().createProcessInstanceQuery();

3）任务查询对象-------processEngine.getTaskService().createTaskQuery();

4）部署查询对象-------processEngine.getRepositoryService().createDeploymentQuery();

### 7：jBPM中的及格对象

1）Deployment----部署对象，对应就是一次部署
2）Execution-----执行对象
3）ProcessDefinition----流程定义对象，一个流程的步骤说明
4）ProcessInstance---流程实例对象，其实就是一个特殊的执行对象，特指从流程开始到结束的最大分支
5）Task----任务对象

## 二：Myeclipse2017集成JBPM7框架。

1：登陆官网 http://downloads.jboss.org/jbpm/release/7.0.0.Final/ 下载 jbpm7 -full 压缩包 

2：在Myeclipse2017安装路径【D:\Mycelipse2017\install】下新建文件夹【jpbm7-plugins】

3）在安装路径下的dropins文件夹下【D:\Mycelipse2017\install\dropins】新建 【jbpm.link】文件，内容为

```
path=D:\\Mycelipse2017\\install\\jpbm7-plugins
```

4）将【jbpm-installer-7.0.0.Final.zip】解压下的【jbpm-installer\lib 】文件夹下，找到找到org.drools.updatesite-7.0.0.Final.zip 将此文件解压 复制【 features 】和【 plugins 】文件到第2步【jbpm7-plugins】文件夹下

5）将j【bpm-installer\lib 】文件夹下【jbpm-7.0.0.Final-bin.zip】解压，新建并复制到【D:\Mycelipse2017\jbpm7.Final-bin\jbpm-7.0.0.Final-bin 】文件夹下

6）重启Myeclipse2017，window——>preferences——>jBPM,然后添加刚刚在【D:\Mycelipse2017\jbpm7.Final-bin\jbpm-7.0.0.Final-bin】文件夹下的内容，结果如图

 

![img](https://images2018.cnblogs.com/blog/1174906/201808/1174906-20180809181640604-1534590942.png)

7）点击Apply——>OK

\8) 新建jBPM工程验证即可。

 三：如何在SSH项目中整合 jbpm 并使用。

1）对于jar包，原有的SSH项目中对于jpbm所依赖的15个jar包其实已经包含了10个，我们只需再考五个即可。

2）配置文件：原来的三个只需拷一个【 jbpm.cfg.xml 】，其他的由其他进行配置，

　　是jbpm.hibernate.cfg.xml

![img](https://images2018.cnblogs.com/blog/1174906/201808/1174906-20180809194757765-109481720.png)

所以在beans.xml文件中配置如下内容即可

```xml
    <!-- 本地会话工厂bean -->
    <bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!-- 注入hibernate属性 -->
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5InnoDBDialect</prop>
                <prop key="hibernate.hbm2ddl.auto">update</prop>
                <prop key="hibernate.show_sql">true</prop>
                 <prop key="hibernate.format_sql">true</prop>
             </props>
         </property>
         <!-- hibernate映射文件的位置 -->
         <property name="mappingDirectoryLocations">
             <list>
                 <value>classpath:cn/xqc/moder/domain</value>
             </list>
         </property>
 <!--                     新加的   strat                 -->
         <!-- 加载jbpm jar包中的hbm映射文件 -->
         <property name="mappingJarLocations">
             <list>
                 <value>/WEB-INF/lib/jbpm.jar</value>
             </list>
         </property>
 <!--                     新加的    end                -->
     </bean>
```

 

　　　　注：Tomact 与 jbpm的EL表达式的包冲突的问题。

3）配置jbpm与Spring整合

整合后我们要将jbpm的控制权交给Spring管理。所以在beans.xml文件中添加如下内容

```xml
     <!-- 配置jbpm和spring框架整合的核心类 -->
     <bean id="springHelper" class="org.jbpm.pvm.internal.processengine.SpringHelper">
         <!-- 指定jbpm框架的核心配置文件 -->
         <property name="jbpmCfg" value="jbpm.cfg.xml"/>
     </bean>

     <!-- 配置ProcessEngine对象 交由Spring进行管理 -->
     <bean id="processEngine" factory-bean="springHelper" factory-method="createProcessEngine"></bean>
```

并且修改我们刚刚拷贝的【jbpm.cfg.xml】文件修改如下

```xml
<?xml version="1.0" encoding="UTF-8"?>

<jbpm-configuration>

  <import resource="jbpm.default.cfg.xml" />
  <import resource="jbpm.businesscalendar.cfg.xml" />
  <!--

  将其改为    jbpm.tx.spring.cfg.xml
   <import resource="jbpm.tx.hibernate.cfg.xml" />

   -->
   <import resource="jbpm.tx.spring.cfg.xml"></import>
   <import resource="jbpm.jpdl.cfg.xml" />
   <import resource="jbpm.bpmn.cfg.xml" />
   <import resource="jbpm.identity.cfg.xml" />

   <!-- Job executor is excluded for running the example test cases. -->
   <!-- To enable timers and messages in production use, this should be included. -->
   <!--
   <import resource="jbpm.jobexecutor.cfg.xml" />
   -->

 </jbpm-configuration>
```

4）可以新建一个 Class 让其获取 ProcessEngine 对象测试项目是否整合完成。还有一个可以看一下jbpm的那18张表是不是在数据库中已经创建了。如果创建了，基本就没问题了。

```java
@Resource
private ProcessEngine processEngine;
```

5）然后就可以操作 Service 进行流程控制了。

 