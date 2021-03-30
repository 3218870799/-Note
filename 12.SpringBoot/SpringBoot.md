# 一、Spring Boot 入门

## 1、Spring Boot 简介

因而 Spring Boot 应用本质上就是一个基于 Spring 框架的应用，它是 Spring 对“约定优先于配置”理念的最佳实践产物，它能够帮助开发者更快速高效地构建基于 Spring 生态圈的应用。自动配置、起步依赖、Actuator

优点：独立运行，简化配置，自动装配

与 Spring 的区别：

## 2、微服务

微服务：架构风格（服务微化）一个应用应该是一组小型服务；可以通过 HTTP 的方式进行互通；

单体应用：ALL IN ONE

微服务：每一个功能元素最终都是一个可独立替换和独立升级的软件单元；

[详细参照微服务文档](https://martinfowler.com/articles/microservices.html#MicroservicesAndSoa)

## 4、Spring Boot HelloWorld

一个功能：浏览器发送 hello 请求，服务器接受请求并处理，响应 Hello World 字符串；

1、创建一个 maven 工程；（jar）

2、导入 spring boot 相关的依赖

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

3、编写一个主程序；启动 Spring Boot 应用

```java

/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {

        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }
}
```

4、编写相关的 Controller、Service

```java
@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World!";
    }
}

```

5、运行主程序测试

6、简化部署

```xml
 <!-- 这个插件，可以将应用打包成一个可执行的jar包；-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

将这个应用打成 jar 包，直接使用 java -jar 的命令进行执行；

### 主程序类

如何在 SpringBoot 启动时运行一定的代码？

你可以实现接口 `ApplicationRunner`或者 `CommandLineRunner`，这两个接口实现方式一样，它们都只提供了一个 run 方法。

如果启动的时候有多个 ApplicationRunner 和 CommandLineRunner，想控制它们的启动顺序，可以实现 `org.springframework.core.Ordered`接口或者使用 `org.springframework.core.annotation.Order`注解。

```java
import org.springframework.boot.*
import org.springframework.stereotype.*
@Component
public class MyBean implements CommandLineRunner
{
	public void run(String... args){
	// Do something...
    }
}
```

# 二、starter 启动器

## 自动配置

在 Spring 程序 main 方法中，添加 ` @SpringBootApplication` 或者@EnableAutoConfiguration 会自动去 maven 中读取每个 starter 中的 `spring.factories` 文件，改文件里配置了所有需要被创建的 Spring 容器中的 bean

1）、SpringBoot 启动的时候加载主配置类，开启了自动配置功能 @EnableAutoConfiguration

2）、@EnableAutoConfiguration 作用：

- 利用 EnableAutoConfigurationImportSelector 给容器中导入一些组件
- 可以查看 selectImports()方法的内容；
- List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);获取候选的配置

```xml
SpringFactoriesLoader.loadFactoryNames()
扫描所有jar包类路径下  META-INF/spring.factories
把扫描到的这些文件的内容包装成properties对象
从properties中获取到EnableAutoConfiguration.class类（类名）对应的值，然后把他们添加在容器中
```

如果我们要禁用特定的自动配置，我们可以使用 @EnableAutoConfiguration 注解的 exclude 属性来指示它。如下禁用了数据源自动配置

将类路径下 META-INF/spring.factories 里面配置的所有 EnableAutoConfiguration 的值加入到了容器中

1、@Conditional 派生注解（Spring 注解版原生的@Conditional 作用）

作用：必须是@Conditional 指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

| @Conditional 扩展注解           | 作用（判断是否满足当前指定条件）                     |
| ------------------------------- | ---------------------------------------------------- |
| @ConditionalOnJava              | 系统的 java 版本是否符合要求                         |
| @ConditionalOnBean              | 容器中存在指定 Bean；                                |
| @ConditionalOnMissingBean       | 容器中不存在指定 Bean；                              |
| @ConditionalOnExpression        | 满足 SpEL 表达式指定                                 |
| @ConditionalOnClass             | 系统中有指定的类                                     |
| @ConditionalOnMissingClass      | 系统中没有指定的类                                   |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的 Bean，或者这个 Bean 是首选 Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                       |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                         |
| @ConditionalOnWebApplication    | 当前是 web 环境                                      |
| @ConditionalOnNotWebApplication | 当前不是 web 环境                                    |
| @ConditionalOnJndi              | JNDI 存在指定项                                      |

**自动配置类必须在一定的条件下才能生效；**

我们怎么知道哪些自动配置类生效；

**==我们可以通过启用 debug=true 属性；来让控制台打印自动配置报告==**，这样我们就可以很方便的知道哪些自动配置类生效；

```java
=========================
AUTO-CONFIGURATION REPORT
=========================


Positive matches:（自动配置类启用的）
-----------------

   DispatcherServletAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet'; @ConditionalOnMissingClass did not find unwanted class (OnClassCondition)
      - @ConditionalOnWebApplication (required) found StandardServletEnvironment (OnWebApplicationCondition)


Negative matches:（没有启动，没有匹配成功的自动配置类）
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'javax.jms.ConnectionFactory', 'org.apache.activemq.ActiveMQConnectionFactory' (OnClassCondition)

   AopAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'org.aspectj.lang.annotation.Aspect', 'org.aspectj.lang.reflect.Advice' (OnClassCondition)

```

## 启动器

启动器是一套方便的依赖没描述符，它可以放在自己的程序中。你可以一站式的获取你所需要的 Spring 和相关技术，而不需要依赖描述符的通过示例代码搜索和复制黏贴的负载。

常见启动器：

spring-boot-starter-web-services - SOAP Web Services

spring-boot-starter-web - Web 和 RESTful 应用程序

spring-boot-starter-test - 单元测试和集成测试

spring-boot-starter-jdbc - 传统的 JDBC

spring-boot-starter-hateoas - 为服务添加 HATEOAS 功能

spring-boot-starter-security - 使用 SpringSecurity 进行身份验证和授权

spring-boot-starter-data-jpa - 带有 Hibeernate 的 Spring Data JPA

spring-boot-starter-data-rest - 使用 Spring Data REST 公布简单的 REST 服务

### 自定义启动器

#### 1：新建项目

并建立两个 module，一个为启动器 module，一个为自动配置 module

启动器 Module

作用：只用来做依赖导入

**启动器命名规范：**

springboot 官方的启动器： spring-boot-starter-XXX 如：spring-boot-starter-jdbc

我们自定义的启动器：XXX-spring-boot-starter 如：sglhello-spring-boot-starter

自动配置 Module

作用：具体实现启动器的业务逻辑

**命名规范：**

XXX-spring-boot-starter-autoconfigurer

XXX 最好跟启动器的 XXX 保持一致！

#### 2：配置启动器依赖

在启动器 Module 的 pom.xml 文件中添加添加对自动配置模块项目的依赖

```xml
    <dependencies>
        <!--引入自动配置模块-->
        <dependency>
            <groupId>com.sglhello.starter</groupId>
            <artifactId>sglhello-spring-boot-starter-autoconfigurer</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>
```

#### 3：配置自动配置模块的依赖

这里我们把 dependencies 里面只留一个最基础的 springboot 对 starter 的支持就行了插件的引用，web 的依赖都去掉

```xml
   <dependencies>
      <!--引入spring-boot-starter；所有starter的基本配置-->
      <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter</artifactId>
      </dependency>
   </dependencies>
```

#### 4：编写自动配置业务逻辑

当其他 SpringBoot 项目引用启动器，因为启动器依赖于自动配置模块，然后也会扫描自动配置模块的类路径下的/META-INF 目录下的 `spring.factories` HelloServiceAutoConfiguration 配置类就会被拿到，然后里面的 helloService() 方法返回的 HelloService 对象就会被创建并且被@Bean 注解注册到 ioc 容器里面，这样 springboot 项目 里面就可以 通过@Autowired 注解使用 HelloService 对象了。

在 Config 目录下创建如下三个文件：

HelloProperties.java

```java
import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "xqc.hello")
public class HelloProperties {

    private String prefix;
    private String suffix;

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }
}
```

HelloService.java

```java
public class HelloService {
    HelloProperties helloProperties;
    public HelloProperties getHelloProperties() {
        return helloProperties;
    }
    public void setHelloProperties(HelloProperties helloProperties) {
        this.helloProperties = helloProperties;
    }
    public String sayHellXqc(String name){
        return helloProperties.getPrefix()+"-" +name + helloProperties.getSuffix();
    }
}
```

HelloServiceAutoConfiguration.java

```java
@Configuration//申明这是一个配置类
@ConditionalOnWebApplication//引用启动器的项目是web应用此自动配置模块才生效
@EnableConfigurationProperties(HelloProperties.class)//加载配置对象到容器
public class HelloServiceAutoConfiguration {

    @Autowired
    HelloProperties helloProperties;
    //方法返回结果对象加载到容器
    @Bean
    public HelloService helloService(){
         //新建业务逻辑处理对象，并返回加载到容器中，
        // 这样引用启动器的项目就可以 @Autowired  HelloService 对象直接使用了
        HelloService service = new HelloService();
        service.setHelloProperties(helloProperties);
        return service;
    }
}

```

#### 5：创建扫描配置文件

因为 springboot 在启动的过程中会去扫描项目和所有项目依赖引用的 jar 包 类路径下的 META-IN 目录下的 spring.factories 配置读取所有的拦截器，过滤器，自动配置 XXXAutoConfiguration 等等。

```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.sgl.mystarter.sglhello.config.HelloServiceAutoConfiguration
```

# 二：常用注解

### @SpringBootApplication

标在主类上，SpringBoot 就应该运行这个类的 main 方法来启动 SpringBoot 应用；主要组合包含了以下 3 个注解：

（1）@Configuration 注解，实现配置文件的功能；

（2）@EnableAutoConfiguration：打开自动配置的功能，也可以关闭某个自动配置的选项，如关闭数据源自动配置的功能：@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})；

它也组合了两个注解：

@AutoConfigurationPackage：自动配置包

@Import(AutoConfigurationPackages.Registrar.class)：导入一个类到 IOC 容器中，类路径为 meta-inf 的 spring.factories 配置进行导入。

（3）@ComponentScan：Spring 组件扫描。

就配置文件自动装配，装配到依赖的类里面，再以动态代理的方式注入到 Spring 容器里

**@RequestMapping 和 @ GetMapping 的区别？**

RequestMapping 具有类属性的，可以进行 GET、POST、PUT 或者其他的注释中具有的请求方法。

GetMapping 是 Get 请求方法中的一个特例，它只是 RequestMapping 的一个延伸，目的是为了提高清晰度。

### @Component

把普通的 pojo 实例化到 spring 绒球中，相当于配置文件中的，泛指各种组件，就是说类不属于@Controller，@Service 等的时候

### @Bean

作用在方法上边，将方法的返回值纳入 spring 管理，告诉 Spring 这个方法将会返回一个对象，这个对象要注册为 Spring 应用上下文中的 bean。

如果想将第三方的类变成组件，你又没有没有源代码，也就没办法使用`@Component`进行自动配置，这种时候使用`@Bean`就比较合适了。不过同样的也可以通过 xml 方式来定义。

另外@Bean 注解的方法返回值是对象，可以在方法中为对象设置属性。

### @Configuration

标注当前类是配置类，并会将当前类内声明的一个或多个以@Bean 注解标记的方法的实例纳入到 srping 容器中，并且实例名就是方法名。

### @Value

### @Repository

用于标注数据访问组件，即 DAO 组件。

### @ComponentScan

组件扫描。相当于，如果扫描到有@Component @Controller @Service 等这些注解的类，则把

这些类注册为 bean。

### @Qualifier

当有多个同一类型的 Bean 时，可以用@Qualifier("name")来指定。与@Autowired 配合使用，@Autowired @Qualifie("userService") 两个结合起来可以根据名字和类型注入

### @Autowired

原理：

当 Spring 容器启动时，注解解析器会被注册到容器中，扫描代码，如果带有@Autowired 注解，则将依赖注入信息封装到 InjectionMetadata 中，创建 Bean 时，会调用各种 BeanPostProcessor 对 bean 初始化，注解解析器 AutowiredAnnotationBeanPostProcessor 负责将相关的依赖注入进来；

### @Resource

和@Autowired 注解都是用来实现依赖注入的。只是@AutoWried 按 by type 自动注入，而@Resource 默认按 byName 自动注入。

@Resource 有两个重要属性，分别是 name 和 type

spring 将 name 属性解析为 bean 的名字，而 type 属性则被解析为 bean 的类型。所以如果使用 name 属性，则使用 byName 的自动注入策略，如果使用 type 属性则使用 byType 的自动注入策略。如果都没有指定，则通过反射机制使用 byName 自动注入策略。

### @Controller

默认是单例的，主要是为了性能，单例不用每次都创建；还有就是不需要多例，只要 controller 中不定义属性，那么单例够用了，如果定义了就会出现竞争访问，则通过注解@Scope（“prototype”）将其设置为多例模式

# 二、配置文件

## 1、配置文件

核心配置文件是 application 和 bootstrap 配置文件

application 配置文件这个容易理解，主要用于 Spring Boot 项目的自动化配置。

bootstrap 配置文件有以下几个应用场景：

- 使用 Spring Cloud Config 配置中心时，这时需要在 bootstrap 配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；
- 一些固定的不能被覆盖的属性；
- 一些加密/解密的场景

SpringBoot 使用一个全局的配置文件，配置文件名是固定的；

application.properties

application.yml

配置文件的作用：修改 SpringBoot 自动配置的默认值；SpringBoot 在底层都给我们自动配置好

标记语言：

以前的配置文件；大多都使用的是 **xxxx.xml**文件；

YAML：**以数据为中心**，比 json、xml 等更适合做配置文件；

YAML：配置例子

```yaml
server:
  port: 8081
```

XML：

```xml
<server>
	<port>8081</port>
</server>
```

## 2、YAML 语法：

### 1、基本语法

k:(空格)v：表示一对键值对（空格必须有）；

以**空格**的缩进来控制层级关系；只要是左对齐的一列数据，都是同一个层级的

```yaml
server:
  port: 8081
  path: /hello
```

属性和值也是大小写敏感；

### 2、值的写法

字面量：普通的值（数字，字符串，布尔）

k: v：字面直接来写；

字符串默认不用加上单引号或者双引号；

""：双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思

name: "zhangsan \n lisi"：输出；zhangsan 换行 lisi

''：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据

name: ‘zhangsan \n lisi’：输出；zhangsan \n lisi

对象、Map（属性和值）（键值对）：

k: v：在下一行来写对象的属性和值的关系；注意缩进

对象还是 k: v 的方式

```yaml
friends:
		lastName: zhangsan
		age: 20
```

行内写法：

```yaml
friends: { lastName: zhangsan, age: 18 }
```

数组（List、Set）：

用- 值表示数组中的一个元素

```yaml
pets:
  - cat
  - dog
  - pig
```

行内写法

```yaml
pets: [cat, dog, pig]
```

## 3、读取配置

配置文件

yml 形式：

```yaml
person:
  lastName: hello
  age: 18
  boss: false
  birth: 2017/12/12
  maps: { k1: v1, k2: 12 }
  lists:
    - lisi
    - zhaoliu
  dog:
    name: 小狗
    age: 12
```

application.properties 形式

```properties
info.address=USA
info.company=Spring
info.degree=high
```

我们可以导入配置文件处理器，以后编写配置就有提示了

```xml
<!--导入配置文件处理器，配置文件进行绑定就会有提示-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-configuration-processor</artifactId>
			<optional>true</optional>
		</dependency>
```

properties 配置文件在 idea 中默认 utf-8 可能会乱码，调整

<img src="media/搜狗截图20180130161620.png" alt="idea配置乱码" style="zoom: 67%;" />

### `@Value`

```java
@Component
public class InfoConfig{
 	@Value("${info.address}")
    private String address;
}
```

### `@ConfigurationProperties`

```java
@Component
@ConfigurationProperties(prefix = "info")
public class InfoConfig{
    private String address;
    private String company;
    private String degree;
}
```

@Value 获取值和@ConfigurationProperties 获取值比较

|                      | @ConfigurationProperties | @Value     |
| -------------------- | ------------------------ | ---------- |
| 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法） | 支持                     | 不支持     |
| SpEL                 | 不支持                   | 支持       |
| JSR303 数据校验      | 支持                     | 不支持     |
| 复杂类型封装         | 支持                     | 不支持     |

配置文件 yml 还是 properties 他们都能获取到值；

如果说，我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用@Value；

如果说，我们专门编写了一个 javaBean 来和配置文件进行映射，我们就直接使用@ConfigurationProperties；

读取指定文件：

### @PropertySource+@Value

资源目录下建立 config/db-config.properties:

```java
@Component
@PropertySource(value = { "config/db-config.properties" })
public class DBConfig {

   @Value("${db.username}")
   private String username;

   @Value("${db.password}")
   private String password;
}
```

### @PropertySource+@ConfigurationProperties

```java
@Component
@ConfigurationProperties(prefix = "db")
@PropertySource(value = { "config/db-config.properties" })
public class DBConfig {

   private String username;
   private String password;
```

@PropertySource：加载指定的配置文件；

@ImportResource：导入 Spring 的配置文件，让配置文件里面的内容生效；

Spring Boot 里面没有 Spring 的配置文件，我们自己编写的配置文件，也不能自动识别；

想让 Spring 的配置文件生效，加载进来；@**ImportResource**标注在一个配置类上

```java
@ImportResource(locations = {"classpath:beans.xml"})
导入Spring的配置文件让其生效
```

编写 Spring 的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="helloService" class="com.xqc.springboot.service.HelloService"></bean>
</beans>
```

SpringBoot 推荐给容器中添加组件的方式；推荐使用全注解的方式

1、配置类**@Configuration**------>Spring 配置文件

2、使用**@Bean**给容器中添加组件

```java
/**
 * @Configuration：指明当前类是一个配置类；就是来替代之前的Spring配置文件
 *
 * 在配置文件中用<bean><bean/>标签添加组件
 *
 */
@Configuration
public class MyAppConfig {

    //将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
    @Bean
    public HelloService helloService02(){
        System.out.println("配置类@Bean给容器中添加组件了...");
        return new HelloService();
    }
}
```

## 4、配置文件占位符

1、随机数

```java
${random.value}、${random.int}、${random.long}
${random.int(10)}、${random.int[1024,65536]}

```

2、占位符获取之前配置的值，如果没有可以是用:指定默认值

```properties
person.last-name=张三${random.uuid}
person.age=${random.int}
person.birth=2017/12/15
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=${person.hello:hello}_dog
person.dog.age=15
```

## 5、Profile

1、多 Profile 文件

我们在主配置文件编写的时候，文件名可以是 application-{profile}.properties/yml

默认使用 application.properties 的配置；

2、yml 支持多文档块方式

```yml
server:
  port: 8081
spring:
  profiles:
    active: prod

---
server:
  port: 8083
spring:
  profiles: dev

---
server:
  port: 8084
spring:
  profiles: prod #指定属于哪个环境
```

3、激活指定 profile

1、在配置文件中指定 spring.profiles.active=dev

2、命令行：

java -jar spring-boot-02-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev；

可以直接在测试的时候，配置传入命令行参数

3、虚拟机参数；

-Dspring.profiles.active=dev

## 6、配置文件加载位置

springboot 启动会扫描以下位置的 application.properties 或者 application.yml 文件作为 Spring boot 的默认配置文件

–file:./config/

–file:./

–classpath:/config/

–classpath:/

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

SpringBoot 会从这四个位置全部加载主配置文件；**互补配置**；

==我们还可以通过 spring.config.location 来改变默认的配置文件位置==

**项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默认加载的这些配置文件共同起作用形成互补配置；**

java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --spring.config.location=G:/application.properties

## 7、配置加载顺序

SpringBoot 也可以从以下位置加载配置；

优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置

1：命令行参数

所有的配置都可以在命令行上进行指定

java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8087 --server.context-path=/abc

多个配置用空格分开； --配置项=值

2：来自 java:comp/env 的 JNDI 属性

3：Java 系统属性（System.getProperties()）

4：操作系统环境变量

5：RandomValuePropertySource 配置的 random.\*属性值

由 jar 包外向 jar 包内进行寻找；优先加载带 profile

**6：jar 包外部的 application-{profile}.properties 或 application.yml(带 spring.profile)配置文件**

**7：jar 包内部的 application-{profile}.properties 或 application.yml(带 spring.profile)配置文件**

再来加载不带 profile

**8.jar 包外部的 application.properties 或 application.yml(不带 spring.profile)配置文件**

**9.jar 包内部的 application.properties 或 application.yml(不带 spring.profile)配置文件**

10：@Configuration 注解类上的@PropertySource

11：通过 SpringApplication.setDefaultProperties 指定的默认属性

# 三、日志

SpringBoot 使用 Slf4j 做日志抽象和 Logback 作为默认日志框架实现

## 1、日志

日志体系

![image-20210104092014718](media/image-20210104092014718.png)

左边选一个门面（抽象层）、右边来选一个实现；

**日志门面**

日志门面定义了一组日志的接口规范，它并不提供底层具体的实现逻辑。`Apache Commons Logging` 和 `Slf4j` 就属于这一类。

**日志实现**

日志实现则是日志具体的实现，包括日志级别控制、日志打印格式、日志输出形式（输出到数据库、输出到文件、输出到控制台等）。`Log4j`、`Log4j2`、`Logback` 以及 `Java Util Logging` 则属于这一类。

SpringBoot：底层是 Spring 框架，Spring 框架默认是用 JCL；‘

**==SpringBoot 选用 SLF4j 和 logback；==**

## 2、SLF4j 使用

### 1、如何在系统中使用 SLF4j https://www.slf4j.org

以后开发的时候，日志记录方法的调用，不应该来直接调用日志的实现类，而是调用日志抽象层里面的方法；

给系统里面导入 slf4j 的 jar 和 logback 的实现 jar

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

图示；

![media/concrete-bindings.png](media/concrete-bindings.png)

每一个日志的实现框架都有自己的配置文件。使用 slf4j 以后，**配置文件还是做成日志实现框架自己本身的配置文件；**

### 2、遗留问题

a（slf4j+logback）: Spring（commons-logging）、Hibernate（jboss-logging）、MyBatis、xxxx

统一日志记录，即使是别的框架和我一起统一使用 slf4j 进行输出？

![](media/legacy.png)

**如何让系统中所有的日志都统一到 slf4j；**

==1、将系统中其他日志框架先排除出去；==

==2、用中间包来替换原有的日志框架；==

==3、我们导入 slf4j 其他的实现==

## 3、SpringBoot 日志关系

```xml
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>
```

SpringBoot 使用它来做日志功能；

```xml
	<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-logging</artifactId>
		</dependency>
```

底层依赖关系

![](media/搜狗截图20180131220946.png)

总结：

1）、SpringBoot 底层也是使用 slf4j+logback 的方式进行日志记录

2）、SpringBoot 也把其他的日志都替换成了 slf4j；

3）、中间替换包？

```java
@SuppressWarnings("rawtypes")
public abstract class LogFactory {

    static String UNSUPPORTED_OPERATION_IN_JCL_OVER_SLF4J = "http://www.slf4j.org/codes.html#unsupported_operation_in_jcl_over_slf4j";

    static LogFactory logFactory = new SLF4JLogFactory();
```

![](media/搜狗截图20180131221411.png)

4）、如果我们要引入其他框架？一定要把这个框架的默认日志依赖移除掉？

Spring 框架用的是 commons-logging；

```xml
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<exclusions>
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
```

**==SpringBoot 能自动适配所有的日志，而且底层使用 slf4j+logback 的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉即可；==**

## 4、日志使用；

### 1、默认配置

SpringBoot 默认帮我们配置好了日志；

```java
	//记录器
	Logger logger = LoggerFactory.getLogger(getClass());
	@Test
	public void contextLoads() {
		//System.out.println();

		//日志的级别；
		//由低到高   trace<debug<info<warn<error
		//可以调整输出的日志级别；日志就只会在这个级别以以后的高级别生效
		logger.trace("这是trace日志...");
		logger.debug("这是debug日志...");
		//SpringBoot默认给我们使用的是info级别的，没有指定级别的就用SpringBoot默认规定的级别；root级别
		logger.info("这是info日志...");
		logger.warn("这是warn日志...");
		logger.error("这是error日志...");


	}
```

        日志输出格式：
    		%d表示日期时间，
    		%thread表示线程名，
    		%-5level：级别从左显示5个字符宽度
    		%logger{50} 表示logger名字最长50个字符，否则按照句点分割。
    		%msg：日志消息，
    		%n是换行符
        -->
        %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n

SpringBoot 修改日志的默认配置

```properties
logging.level.com.xqc=trace


#logging.path=
# 不指定路径在当前项目下生成springboot.log日志
# 可以指定完整的路径；
#logging.file=G:/springboot.log

# 在当前磁盘的根路径下创建spring文件夹和里面的log文件夹；使用 spring.log 作为默认文件
logging.path=/spring/log

#  在控制台输出的日志的格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
# 指定文件中日志输出的格式
logging.pattern.file=%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} ==== %msg%n
```

| logging.file | logging.path | Example  | Description                        |
| ------------ | ------------ | -------- | ---------------------------------- |
| (none)       | (none)       |          | 只在控制台输出                     |
| 指定文件名   | (none)       | my.log   | 输出日志到 my.log 文件             |
| (none)       | 指定目录     | /var/log | 输出到指定目录的 spring.log 文件中 |

### 2、指定配置

给类路径下放上每个日志框架自己的配置文件即可；SpringBoot 就不使用他默认配置的了

| Logging System          | Customization                                                                    |
| ----------------------- | -------------------------------------------------------------------------------- |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml` or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                                              |
| JDK (Java Util Logging) | `logging.properties`                                                             |

logback.xml：直接就被日志框架识别了；

**logback-spring.xml**：日志框架就不直接加载日志的配置项，由 SpringBoot 解析日志配置，可以使用 SpringBoot 的高级 Profile 功能

```xml
<springProfile name="staging">
    <!-- configuration to be enabled when the "staging" profile is active -->
  	可以指定某段配置只在某个环境下生效
</springProfile>

```

如：

```xml
<appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <!--
        日志输出格式：
			%d表示日期时间，
			%thread表示线程名，
			%-5level：级别从左显示5个字符宽度
			%logger{50} 表示logger名字最长50个字符，否则按照句点分割。
			%msg：日志消息，
			%n是换行符
        -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <springProfile name="dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ----> [%thread] ---> %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
            <springProfile name="!dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ==== [%thread] ==== %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
        </layout>
    </appender>
```

如果使用 logback.xml 作为日志配置文件，还要使用 profile 功能，会有以下错误

`no applicable action for [springProfile]`

## 5、切换日志框架

可以按照 slf4j 的日志适配图，进行相关的切换；

slf4j+log4j 的方式；

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <exclusions>
    <exclusion>
      <artifactId>logback-classic</artifactId>
      <groupId>ch.qos.logback</groupId>
    </exclusion>
    <exclusion>
      <artifactId>log4j-over-slf4j</artifactId>
      <groupId>org.slf4j</groupId>
    </exclusion>
  </exclusions>
</dependency>

<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
</dependency>

```

切换为 log4j2

```xml
   <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-logging</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
        </dependency>

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

# 四：模板引擎 Thymeleaf

JSP、Velocity、Freemarker、Thymeleaf 都是常见的模板引擎。

Thymeleaf 是新一代 Java 模板引擎，Thymeleaf 支持 HTML 原型。它既可以让前端工程师在浏览器中直接打开查看样式，也可以让后端工程师结合真实数据查看显示效果，同时，SpringBoot 提供了 Thymeleaf 自动化配置解决方案，因此在 SpringBoot 中使用 Thymeleaf 非常方便。

## 使用

### 1、引入 thymeleaf

```xml
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
          	2.1.6
		</dependency>
切换thymeleaf版本
<properties>
		<thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>
		<!-- 布局功能的支持程序  thymeleaf3主程序  layout2以上版本 -->
		<!-- thymeleaf2   layout1-->
		<thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>
  </properties>
```

导入 thymeleaf 的名称空间

```xml
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

使用 thymeleaf 语法；

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body>
    <h1>成功！</h1>
    <!--th:text 将div里面的文本内容设置为 -->
    <div th:text="${hello}">这是显示欢迎信息</div>
  </body>
</html>
```

## 语法

### 简单表达式

变量表达式：${……}

消息表达式：#{……}

选择表达式：\*{……}

![img](media\clip_image004.jpg)

链接表达式：@{……}

分段表达式：th:insert 或者 th:replace

![img](media\clip_image006.jpg)

**`~{...}`**

片段表达式是 Thymeleaf 的特色之一，细粒度可以达到标签级别，这是 JSP 无法做到的。片段表达式拥有三种语法：

- `~{ viewName }`：表示引入完整页面
- `~{ viewName ::selector}`：表示在指定页面寻找片段，其中 selector 可为片段名、jquery 选择器等
- `~{ ::selector}`：表示在当前页寻找

### 字面量

这些是一些可以直接写在表达式中的字符，主要有如下几种：

- 文本字面量：'one text', 'Another one!',…
- 数字字面量：0, 34, 3.0, 12.3,…
- 布尔字面量：true, false
- Null 字面量：null
- 字面量标记：one, sometext, main,…

### 文本运算

文本可以使用 `+` 进行拼接。

如果字符串中包含变量，也可以使用另一种简单的方式，叫做字面量置换，用 `|` 代替 `'...' + '...'`，如下：

```html
<div th:text="|hello ${user.username}|"></div>
<div th:text="'hello '+${user.username}+' '+|Go ${user.address}|"></div>
```

比较与相等

表达式里的值可以使用 `>`, `<`, `>=` 和 `<=` 符号比较。`==` 和 `!=` 运算符用于检查相等（或者不相等）。注意 `XML`规定 `<` 和 `>` 标签不能用于属性值，所以应当把它们转义为 `<` 和 `>`。

如果不想转义，也可以使用别名：gt (>)；lt (<)；ge (>=)；le (<=)；not (!)。还有 eq (==), neq/ne (!=)。

```html
<div th:with="age=(99*99/99+99-1)">
  <div th:text="${age} eq 197"></div>
  <div th:text="${age} ne 197"></div>
  <div th:text="${age} ge 197"></div>
  <div th:text="${age} gt 197"></div>
  <div th:text="${age} le 197"></div>
  <div th:text="${age} lt 197"></div>
</div>
```

### 内置对象

基本内置对象：

- \#ctx：上下文对象。
- \#vars: 上下文变量。
- \#locale：上下文区域设置。
- \#request：（仅在 Web 上下文中）HttpServletRequest 对象。
- \#response：（仅在 Web 上下文中）HttpServletResponse 对象。
- \#session：（仅在 Web 上下文中）HttpSession 对象。
- \#servletContext：（仅在 Web 上下文中）ServletContext 对象。

```html
<div th:text='${#session.getAttribute("name")}'></div>
```

### 遍历

数组/集合/Map/Enumeration/Iterator 等的遍历也算是一个非常常见的需求，Thymeleaf 中通过 th:each 来实现遍历，像下面这样：

```html
<table border="1">
  <tr th:each="u : ${users}">
    <td th:text="${u.username}"></td>
    <td th:text="${u.address}"></td>
  </tr>
</table>
```

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

当然，Thymeleaf 不仅仅能在 Spring Boot 中使用，也可以使用在其他地方，只不过 Spring Boot 针对 Thymeleaf 提供了一整套的自动化配置方案，这一套配置类的属性在 `org.springframework.boot.autoconfigure.thymeleaf.ThymeleafProperties` 中

# 五：Web 开发

## 2、静态资源

在源文件夹下，创建一个名为 static 的文件夹。然后，你可以把你的静态的内容放在这里面。

```java
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
public class ResourceProperties implements ResourceLoaderAware {
  //可以设置和静态资源有关的参数，缓存时间等
```

```java
	WebMvcAuotConfiguration：
		@Override
		public void addResourceHandlers(ResourceHandlerRegistry registry) {
			if (!this.resourceProperties.isAddMappings()) {
				logger.debug("Default resource handling disabled");
				return;
			}
			Integer cachePeriod = this.resourceProperties.getCachePeriod();
			if (!registry.hasMappingForPattern("/webjars/**")) {
				customizeResourceHandlerRegistration(
						registry.addResourceHandler("/webjars/**")
								.addResourceLocations(
										"classpath:/META-INF/resources/webjars/")
						.setCachePeriod(cachePeriod));
			}
			String staticPathPattern = this.mvcProperties.getStaticPathPattern();
          	//静态资源文件夹映射
			if (!registry.hasMappingForPattern(staticPathPattern)) {
				customizeResourceHandlerRegistration(
						registry.addResourceHandler(staticPathPattern)
								.addResourceLocations(
										this.resourceProperties.getStaticLocations())
						.setCachePeriod(cachePeriod));
			}
		}

        //配置欢迎页映射
		@Bean
		public WelcomePageHandlerMapping welcomePageHandlerMapping(
				ResourceProperties resourceProperties) {
			return new WelcomePageHandlerMapping(resourceProperties.getWelcomePage(),
					this.mvcProperties.getStaticPathPattern());
		}

       //配置喜欢的图标
		@Configuration
		@ConditionalOnProperty(value = "spring.mvc.favicon.enabled", matchIfMissing = true)
		public static class FaviconConfiguration {

			private final ResourceProperties resourceProperties;

			public FaviconConfiguration(ResourceProperties resourceProperties) {
				this.resourceProperties = resourceProperties;
			}

			@Bean
			public SimpleUrlHandlerMapping faviconHandlerMapping() {
				SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
				mapping.setOrder(Ordered.HIGHEST_PRECEDENCE + 1);
              	//所有  **/favicon.ico
				mapping.setUrlMap(Collections.singletonMap("**/favicon.ico",
						faviconRequestHandler()));
				return mapping;
			}

			@Bean
			public ResourceHttpRequestHandler faviconRequestHandler() {
				ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
				requestHandler
						.setLocations(this.resourceProperties.getFaviconLocations());
				return requestHandler;
			}

		}

```

==1）、所有 /webjars/\*\* ，都去 classpath:/META-INF/resources/webjars/ 找资源；==

webjars：以 jar 包的方式引入静态资源；

http://www.webjars.org/

![](media/搜狗截图20180203181751.png)

localhost:8080/webjars/jquery/3.3.1/jquery.js

```xml
<!--引入jquery-webjar-->在访问的时候只需要写webjars下面资源的名称即可
		<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>jquery</artifactId>
			<version>3.3.1</version>
		</dependency>
```

==2）、"/\*\*" 访问当前项目的任何资源，都去（静态资源的文件夹）找映射==

```
"classpath:/META-INF/resources/",
"classpath:/resources/",
"classpath:/static/",
"classpath:/public/"
"/"：当前项目的根路径
```

localhost:8080/abc === 去静态资源文件夹里面找 abc

==3）、欢迎页； 静态资源文件夹下的所有 index.html 页面；被"/\*\*"映射；==

localhost:8080/ 找 index 页面

==4）、所有的 \*\*/favicon.ico 都是在静态资源文件下找；==

## 3：MVC 结构框架

SpringBoot 提供了 SpringMVC 的自动配置。

（1）自动配置了 ViewResolver（视图解析器：根据方法的返回值得到视图对象（View），视图对象决定如何渲染（转发？重定向？））我们可以自己给容器添加一个视图解析器，会自动组合进来。

(2)自动注册了 `Converter`（转换器，类型转换）, `GenericConverter`（格式化器）, `Formatter` beans.

==自己添加的格式化器转换器，我们只需要放在容器中即可==

（3）自动配置了`HttpMessageConverters`

SpringMVC 用来转换 Http 请求和响应的；User 转换成 Json；`HttpMessageConverters` 是从容器中确定；获取所有的 HttpMessageConverter；

==自己给容器中添加 HttpMessageConverter，只需要将自己的组件注册容器中（@Bean,@Component）==

#### **如何修改 SpringBoot 的默认配置**

模式：

1）、SpringBoot 在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean、@Component）如果有就用用户配置的，如果没有，才自动配置；如果有些组件可以有多个（ViewResolver）将用户配置的和自己默认的组合起来；

2）、在 SpringBoot 中会有非常多的 xxxConfigurer 帮助我们进行扩展配置

3）、在 SpringBoot 中会有很多的 xxxCustomizer 帮助我们进行定制配置

#### 扩展 SpringMVC

**==编写一个配置类（@Configuration），是 WebMvcConfigurerAdapter 类型；不能标注@EnableWebMvc==**;

既保留了所有的自动配置，也能用我们扩展的配置；

```java
//使用WebMvcConfigurerAdapter可以来扩展SpringMVC的功能
@Configuration
public class MyMvcConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) 	{
       // super.addViewControllers(registry);
        //浏览器发送 /xqc 请求来到 success
        registry.addViewController("/xqc").setViewName("success");
    }
}
```

原理：

1）、WebMvcAutoConfiguration 是 SpringMVC 的自动配置类

2）、在做其他自动配置时会导入；

@Import(EnableWebMvcConfiguration.class)

3）、容器中所有的 WebMvcConfigurer 都会一起起作用；

4）、我们的配置类也会被调用；

#### 全面接管 SpringMVC

SpringBoot 对 SpringMVC 的自动配置不需要了，所有都是我们自己配置；所有的 SpringMVC 的自动配置都失效了

**我们需要在配置类中添加@EnableWebMvc 即可；**

## 6、RestfulCRUD

### 1）、默认访问首页

```java

//使用WebMvcConfigurerAdapter可以来扩展SpringMVC的功能
//@EnableWebMvc   不要接管SpringMVC
@Configuration
public class MyMvcConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
       // super.addViewControllers(registry);
        //浏览器发送 /xqc 请求来到 success
        registry.addViewController("/xqc").setViewName("success");
    }

    //所有的WebMvcConfigurerAdapter组件都会一起起作用
    @Bean //将组件注册在容器
    public WebMvcConfigurerAdapter webMvcConfigurerAdapter(){
        WebMvcConfigurerAdapter adapter = new WebMvcConfigurerAdapter() {
            @Override
            public void addViewControllers(ViewControllerRegistry registry) {
                registry.addViewController("/").setViewName("login");
                registry.addViewController("/index.html").setViewName("login");
            }
        };
        return adapter;
    }
}

```

### 2）、国际化

**1）、编写国际化配置文件；**

2）、使用 ResourceBundleMessageSource 管理国际化资源文件

3）、在页面使用 fmt:message 取出国际化内容

步骤：

1）、编写国际化配置文件，抽取页面需要显示的国际化消息

![](media/搜狗截图20180211130721.png)

2）、SpringBoot 自动配置好了管理国际化资源文件的组件；

```java
@ConfigurationProperties(prefix = "spring.messages")
public class MessageSourceAutoConfiguration {

    /**
	 * Comma-separated list of basenames (essentially a fully-qualified classpath
	 * location), each following the ResourceBundle convention with relaxed support for
	 * slash based locations. If it doesn't contain a package qualifier (such as
	 * "org.mypackage"), it will be resolved from the classpath root.
	 */
	private String basename = "messages";
    //我们的配置文件可以直接放在类路径下叫messages.properties；

    @Bean
	public MessageSource messageSource() {
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		if (StringUtils.hasText(this.basename)) {
            //设置国际化资源文件的基础名（去掉语言国家代码的）
			messageSource.setBasenames(StringUtils.commaDelimitedListToStringArray(
					StringUtils.trimAllWhitespace(this.basename)));
		}
		if (this.encoding != null) {
			messageSource.setDefaultEncoding(this.encoding.name());
		}
		messageSource.setFallbackToSystemLocale(this.fallbackToSystemLocale);
		messageSource.setCacheSeconds(this.cacheSeconds);
		messageSource.setAlwaysUseMessageFormat(this.alwaysUseMessageFormat);
		return messageSource;
	}
```

3）、去页面获取国际化的值；

![](media/搜狗截图20180211134506.png)

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, shrink-to-fit=no"
    />
    <meta name="description" content="" />
    <meta name="author" content="" />
    <title>Signin Template for Bootstrap</title>
    <!-- Bootstrap core CSS -->
    <link
      href="asserts/css/bootstrap.min.css"
      th:href="@{/webjars/bootstrap/4.0.0/css/bootstrap.css}"
      rel="stylesheet"
    />
    <!-- Custom styles for this template -->
    <link
      href="asserts/css/signin.css"
      th:href="@{/asserts/css/signin.css}"
      rel="stylesheet"
    />
  </head>

  <body class="text-center">
    <form class="form-signin" action="dashboard.html">
      <img
        class="mb-4"
        th:src="@{/asserts/img/bootstrap-solid.svg}"
        src="asserts/img/bootstrap-solid.svg"
        alt=""
        width="72"
        height="72"
      />
      <h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">
        Please sign in
      </h1>
      <label class="sr-only" th:text="#{login.username}">Username</label>
      <input
        type="text"
        class="form-control"
        placeholder="Username"
        th:placeholder="#{login.username}"
        required=""
        autofocus=""
      />
      <label class="sr-only" th:text="#{login.password}">Password</label>
      <input
        type="password"
        class="form-control"
        placeholder="Password"
        th:placeholder="#{login.password}"
        required=""
      />
      <div class="checkbox mb-3">
        <label>
          <input type="checkbox" value="remember-me" /> [[#{login.remember}]]
        </label>
      </div>
      <button
        class="btn btn-lg btn-primary btn-block"
        type="submit"
        th:text="#{login.btn}"
      >
        Sign in
      </button>
      <p class="mt-5 mb-3 text-muted">© 2017-2018</p>
      <a class="btn btn-sm">中文</a>
      <a class="btn btn-sm">English</a>
    </form>
  </body>
</html>
```

效果：根据浏览器语言设置的信息切换了国际化；

原理：

国际化 Locale（区域信息对象）；LocaleResolver（获取区域信息对象）；

```java
		@Bean
		@ConditionalOnMissingBean
		@ConditionalOnProperty(prefix = "spring.mvc", name = "locale")
		public LocaleResolver localeResolver() {
			if (this.mvcProperties
					.getLocaleResolver() == WebMvcProperties.LocaleResolver.FIXED) {
				return new FixedLocaleResolver(this.mvcProperties.getLocale());
			}
			AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
			localeResolver.setDefaultLocale(this.mvcProperties.getLocale());
			return localeResolver;
		}
默认的就是根据请求头带来的区域信息获取Locale进行国际化
```

4）、点击链接切换国际化

```java
/**
 * 可以在连接上携带区域信息
 */
public class MyLocaleResolver implements LocaleResolver {

    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        String l = request.getParameter("l");
        Locale locale = Locale.getDefault();
        if(!StringUtils.isEmpty(l)){
            String[] split = l.split("_");
            locale = new Locale(split[0],split[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {

    }
}


 @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }
}


```

### 3）、登陆

开发期间模板引擎页面修改以后，要实时生效

1）、禁用模板引擎的缓存

```
# 禁用缓存
spring.thymeleaf.cache=false
```

2）、页面修改完成以后 ctrl+f9：重新编译；

登陆错误消息的显示

```html
<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
```

### 4）、拦截器进行登陆检查

拦截器

```java

/**
 * 登陆检查，
 */
public class LoginHandlerInterceptor implements HandlerInterceptor {
    //目标方法执行之前
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object user = request.getSession().getAttribute("loginUser");
        if(user == null){
            //未登陆，返回登陆页面
            request.setAttribute("msg","没有权限请先登陆");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }else{
            //已登陆，放行请求
            return true;
        }

    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}

```

注册拦截器

```java
  //所有的WebMvcConfigurerAdapter组件都会一起起作用
    @Bean //将组件注册在容器
    public WebMvcConfigurerAdapter webMvcConfigurerAdapter(){
        WebMvcConfigurerAdapter adapter = new WebMvcConfigurerAdapter() {
            @Override
            public void addViewControllers(ViewControllerRegistry registry) {
                registry.addViewController("/").setViewName("login");
                registry.addViewController("/index.html").setViewName("login");
                registry.addViewController("/main.html").setViewName("dashboard");
            }

            //注册拦截器
            @Override
            public void addInterceptors(InterceptorRegistry registry) {
                //super.addInterceptors(registry);
                //静态资源；  *.css , *.js
                //SpringBoot已经做好了静态资源映射
                registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
                        .excludePathPatterns("/index.html","/","/user/login");
            }
        };
        return adapter;
    }
```

### 5）、CRUD-员工列表

实验要求：

1）、RestfulCRUD：CRUD 满足 Rest 风格；

URI： /资源名称/资源标识 HTTP 请求方式区分对资源 CRUD 操作

|      | 普通 CRUD（uri 来区分操作） | RestfulCRUD       |
| ---- | --------------------------- | ----------------- |
| 查询 | getEmp                      | emp---GET         |
| 添加 | addEmp?xxx                  | emp---POST        |
| 修改 | updateEmp?id=xxx&xxx=xx     | emp/{id}---PUT    |
| 删除 | deleteEmp?id=1              | emp/{id}---DELETE |

2）、实验的请求架构;

| 实验功能                             | 请求 URI | 请求方式 |
| ------------------------------------ | -------- | -------- |
| 查询所有员工                         | emps     | GET      |
| 查询某个员工(来到修改页面)           | emp/1    | GET      |
| 来到添加页面                         | emp      | GET      |
| 添加员工                             | emp      | POST     |
| 来到修改页面（查出员工进行信息回显） | emp/1    | GET      |
| 修改员工                             | emp      | PUT      |
| 删除员工                             | emp/1    | DELETE   |

3）、员工列表：

#### thymeleaf 公共页面元素抽取

```html
1、抽取公共片段
<div th:fragment="copy">&copy; 2011 The Good Thymes Virtual Grocery</div>

2、引入公共片段
<div th:insert="~{footer :: copy}"></div>
~{templatename::selector}：模板名::选择器
~{templatename::fragmentname}:模板名::片段名 3、默认效果：
insert的公共片段在div标签中 如果使用th:insert等属性进行引入，可以不用写~{}：
行内写法可以加上：[[~{}]];[(~{})]；
```

三种引入公共片段的 th 属性：

**th:insert**：将公共片段整个插入到声明引入的元素中

**th:replace**：将声明引入的元素替换为公共片段

**th:include**：将被引入的片段的内容包含进这个标签中

```html
<footer th:fragment="copy">&copy; 2011 The Good Thymes Virtual Grocery</footer>

引入方式
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>

效果
<div>
  <footer>&copy; 2011 The Good Thymes Virtual Grocery</footer>
</div>

<footer>&copy; 2011 The Good Thymes Virtual Grocery</footer>

<div>&copy; 2011 The Good Thymes Virtual Grocery</div>
```

引入片段的时候传入参数：

```html
<nav class="col-md-2 d-none d-md-block bg-light sidebar" id="sidebar">
  <div class="sidebar-sticky">
    <ul class="nav flex-column">
      <li class="nav-item">
        <a
          class="nav-link active"
          th:class="${activeUri=='main.html'?'nav-link active':'nav-link'}"
          href="#"
          th:href="@{/main.html}"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
            class="feather feather-home"
          >
            <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
            <polyline points="9 22 9 12 15 12 15 22"></polyline>
          </svg>
          Dashboard <span class="sr-only">(current)</span>
        </a>
      </li>

      <!--引入侧边栏;传入参数-->
      <div th:replace="commons/bar::#sidebar(activeUri='emps')"></div>
    </ul>
  </div>
</nav>
```

### 6）、CRUD-员工添加

添加页面

```html
<form>
  <div class="form-group">
    <label>LastName</label>
    <input type="text" class="form-control" placeholder="zhangsan" />
  </div>
  <div class="form-group">
    <label>Email</label>
    <input type="email" class="form-control" placeholder="zhangsan@xqc.com" />
  </div>
  <div class="form-group">
    <label>Gender</label><br />
    <div class="form-check form-check-inline">
      <input class="form-check-input" type="radio" name="gender" value="1" />
      <label class="form-check-label">男</label>
    </div>
    <div class="form-check form-check-inline">
      <input class="form-check-input" type="radio" name="gender" value="0" />
      <label class="form-check-label">女</label>
    </div>
  </div>
  <div class="form-group">
    <label>department</label>
    <select class="form-control">
      <option>1</option>
      <option>2</option>
      <option>3</option>
      <option>4</option>
      <option>5</option>
    </select>
  </div>
  <div class="form-group">
    <label>Birth</label>
    <input type="text" class="form-control" placeholder="zhangsan" />
  </div>
  <button type="submit" class="btn btn-primary">添加</button>
</form>
```

提交的数据格式不对：生日：日期；

2017-12-12；2017/12/12；2017.12.12；

日期的格式化；SpringMVC 将页面提交的值需要转换为指定的类型;

2017-12-12---Date； 类型转换，格式化;

默认日期是按照/的方式；

### 7）、CRUD-员工修改

修改添加二合一表单

```html
<!--需要区分是员工修改还是添加；-->
<form th:action="@{/emp}" method="post">
  <!--发送put请求修改员工数据-->
  <!--
1、SpringMVC中配置HiddenHttpMethodFilter;（SpringBoot自动配置好的）
2、页面创建一个post表单
3、创建一个input项，name="_method";值就是我们指定的请求方式
-->
  <input type="hidden" name="_method" value="put" th:if="${emp!=null}" />
  <input type="hidden" name="id" th:if="${emp!=null}" th:value="${emp.id}" />
  <div class="form-group">
    <label>LastName</label>
    <input
      name="lastName"
      type="text"
      class="form-control"
      placeholder="zhangsan"
      th:value="${emp!=null}?${emp.lastName}"
    />
  </div>
  <div class="form-group">
    <label>Email</label>
    <input
      name="email"
      type="email"
      class="form-control"
      placeholder="zhangsan@xqc.com"
      th:value="${emp!=null}?${emp.email}"
    />
  </div>
  <div class="form-group">
    <label>Gender</label><br />
    <div class="form-check form-check-inline">
      <input
        class="form-check-input"
        type="radio"
        name="gender"
        value="1"
        th:checked="${emp!=null}?${emp.gender==1}"
      />
      <label class="form-check-label">男</label>
    </div>
    <div class="form-check form-check-inline">
      <input
        class="form-check-input"
        type="radio"
        name="gender"
        value="0"
        th:checked="${emp!=null}?${emp.gender==0}"
      />
      <label class="form-check-label">女</label>
    </div>
  </div>
  <div class="form-group">
    <label>department</label>
    <!--提交的是部门的id-->
    <select class="form-control" name="department.id">
      <option
        th:selected="${emp!=null}?${dept.id == emp.department.id}"
        th:value="${dept.id}"
        th:each="dept:${depts}"
        th:text="${dept.departmentName}"
      >
        1
      </option>
    </select>
  </div>
  <div class="form-group">
    <label>Birth</label>
    <input
      name="birth"
      type="text"
      class="form-control"
      placeholder="zhangsan"
      th:value="${emp!=null}?${#dates.format(emp.birth, 'yyyy-MM-dd HH:mm')}"
    />
  </div>
  <button
    type="submit"
    class="btn btn-primary"
    th:text="${emp!=null}?'修改':'添加'"
  >
    添加
  </button>
</form>
```

### 8）、CRUD-员工删除

```html
<tr th:each="emp:${emps}">
  <td th:text="${emp.id}"></td>
  <td>[[${emp.lastName}]]</td>
  <td th:text="${emp.email}"></td>
  <td th:text="${emp.gender}==0?'女':'男'"></td>
  <td th:text="${emp.department.departmentName}"></td>
  <td th:text="${#dates.format(emp.birth, 'yyyy-MM-dd HH:mm')}"></td>
  <td>
    <a class="btn btn-sm btn-primary" th:href="@{/emp/}+${emp.id}">编辑</a>
    <button
      th:attr="del_uri=@{/emp/}+${emp.id}"
      class="btn btn-sm btn-danger deleteBtn"
    >
      删除
    </button>
  </td>
</tr>

<script>
  $(".deleteBtn").click(function () {
    //删除当前员工的
    $("#deleteEmpForm").attr("action", $(this).attr("del_uri")).submit();
    return false;
  });
</script>
```

## 7、错误处理机制

### 1）、SpringBoot 默认的错误处理机制

默认效果：

1）、浏览器，返回一个默认的错误页面

![](media/搜狗截图20180226173408.png)

浏览器发送请求的请求头：

![](media/搜狗截图20180226180347.png)

2）、如果是其他客户端，默认响应一个 json 数据

![](media/搜狗截图20180226173527.png)

![](media/搜狗截图20180226180504.png)

原理：

可以参照 ErrorMvcAutoConfiguration；错误处理的自动配置；SpringBoot 给容器中添加了以下组件

步骤：

一但系统出现 4xx 或者 5xx 之类的错误；ErrorPageCustomizer 就会生效（定制错误的响应规则）；就会来到/error 请求；就会被 BasicErrorController 处理；

ErrorPageCustomizer：

```java
	@Value("${error.path:/error}")
	private String path = "/error";  系统出现错误以后来到error请求进行处理；（web.xml注册的错误页面规则）
```

BasicErrorController：处理默认/error 请求

```java
@Controller
@RequestMapping("${server.error.path:${error.path:/error}}")
public class BasicErrorController extends AbstractErrorController {
    //里面两个方法，一个处理浏览器请求，一个处理客户端请求，区别在与浏览器发送的请求有请求头
    //所有的ErrorViewResolver得到ModelAndView
    //去哪个页面是由**DefaultErrorViewResolver**解析得到的；
}
```

去哪个页面是由**DefaultErrorViewResolver**解析得到的；

```java
//默认SpringBoot可以去找到一个页面？  error/404
//模板引擎可以解析这个页面地址就用模板引擎解析

//模板引擎可用的情况下返回到errorViewName指定的视图地址

//模板引擎不可用，就在静态资源文件夹下找errorViewName对应的页面   error/404.html
```

### 2）、如果定制错误响应：

#### **1）、如何定制错误的页面；**

- **有模板引擎的情况下；error/状态码;** 【将错误页面命名为 错误状态码.html 放在模板引擎文件夹里面的 error 文件夹下】，发生此状态码的错误就会来到 对应的页面；我们可以使用 4xx 和 5xx 作为错误页面的文件名来匹配这种类型的所有错误，精确优先（优先寻找精确的状态码.html）；

页面能获取的信息；
timestamp：时间戳
status：状态码
error：错误提示
exception：异常对象
message：异常消息
errors：JSR303 数据校验的错误都在这里

- 没有模板引擎（模板引擎找不到这个错误页面），静态资源文件夹下找；

- 以上都没有错误页面，就是默认来到 SpringBoot 默认的错误提示页面；

#### 2）、如何定制错误的 json 数据；

1）、自定义异常处理&返回定制 json 数据；

```java
@ControllerAdvice
public class MyExceptionHandler {

    @ResponseBody
    @ExceptionHandler(UserNotExistException.class)
    public Map<String,Object> handleException(Exception e){
        Map<String,Object> map = new HashMap<>();
        map.put("code","user.notexist");
        map.put("message",e.getMessage());
        return map;
    }
}
//没有自适应效果...
```

2）、转发到/error 进行自适应响应效果处理

```java
 @ExceptionHandler(UserNotExistException.class)
    public String handleException(Exception e, HttpServletRequest request){
        Map<String,Object> map = new HashMap<>();
        //传入我们自己的错误状态码  4xx 5xx，否则就不会进入定制错误页面的解析流程
        /**
         * Integer statusCode = (Integer) request
         .getAttribute("javax.servlet.error.status_code");
         */
        request.setAttribute("javax.servlet.error.status_code",500);
        map.put("code","user.notexist");
        map.put("message",e.getMessage());
        //转发到/error
        return "forward:/error";
    }
```

#### 3）、将我们的定制数据携带出去；

出现错误以后，会来到/error 请求，会被 BasicErrorController 处理，响应出去可以获取的数据是由 getErrorAttributes 得到的（是 AbstractErrorController（ErrorController）规定的方法）；

1、完全来编写一个 ErrorController 的实现类【或者是编写 AbstractErrorController 的子类】，放在容器中；

2、页面上能用的数据，或者是 json 返回能用的数据都是通过 errorAttributes.getErrorAttributes 得到；

容器中 DefaultErrorAttributes.getErrorAttributes()；默认进行数据处理的；

自定义 ErrorAttributes

```java
//给容器中加入我们自己定义的ErrorAttributes
@Component
public class MyErrorAttributes extends DefaultErrorAttributes {

    @Override
    public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes, boolean includeStackTrace) {
        Map<String, Object> map = super.getErrorAttributes(requestAttributes, includeStackTrace);
        map.put("company","xqc");
        return map;
    }
}
```

最终的效果：响应是自适应的，可以通过定制 ErrorAttributes 改变需要返回的内容，

![](media/搜狗截图20180228135513.png)

## 8、配置嵌入式 Servlet 容器

SpringBoot 默认使用 Tomcat 作为嵌入式的 Servlet 容器（Tomact）；

![](media/搜狗截图20180301142915.png)

问题？

### 1）、如何定制和修改 Servlet 容器的相关配置；

方法一、修改和 server 有关的配置（ServerProperties【也是 EmbeddedServletContainerCustomizer】）；

```properties
server.port=8081
server.context-path=/crud

server.tomcat.uri-encoding=UTF-8

//通用的Servlet容器设置
server.xxx
//Tomcat的设置
server.tomcat.xxx
```

方法二、编写一个**EmbeddedServletContainerCustomizer**：嵌入式的 Servlet 容器的定制器；来修改 Servlet 容器的配置

```java
@Bean  //一定要将这个定制器加入到容器中
public EmbeddedServletContainerCustomizer embeddedServletContainerCustomizer(){
    return new EmbeddedServletContainerCustomizer() {

        //定制嵌入式的Servlet容器相关的规则
        @Override
        public void customize(ConfigurableEmbeddedServletContainer container) {
            container.setPort(8083);
        }
    };
}
```

### 2）、注册 Servlet 三大组件【Servlet、Filter、Listener】

由于 SpringBoot 默认是以 jar 包的方式启动嵌入式的 Servlet 容器来启动 SpringBoot 的 web 应用，没有 web.xml 文件。

注册三大组件用以下方式

ServletRegistrationBean

```java
//注册三大组件
@Bean
public ServletRegistrationBean myServlet(){
    ServletRegistrationBean registrationBean = new ServletRegistrationBean(new MyServlet(),"/myServlet");
    return registrationBean;
}

```

FilterRegistrationBean

```java
@Bean
public FilterRegistrationBean myFilter(){
    FilterRegistrationBean registrationBean = new FilterRegistrationBean();
    registrationBean.setFilter(new MyFilter());
    registrationBean.setUrlPatterns(Arrays.asList("/hello","/myServlet"));
    return registrationBean;
}
```

ServletListenerRegistrationBean

```java
@Bean
public ServletListenerRegistrationBean myListener(){
    ServletListenerRegistrationBean<MyListener> registrationBean = new ServletListenerRegistrationBean<>(new MyListener());
    return registrationBean;
}
```

SpringBoot 帮我们自动 SpringMVC 的时候，自动的注册 SpringMVC 的前端控制器；DIspatcherServlet；

DispatcherServletAutoConfiguration 中：

```java
@Bean(name = DEFAULT_DISPATCHER_SERVLET_REGISTRATION_BEAN_NAME)
@ConditionalOnBean(value = DispatcherServlet.class, name = DEFAULT_DISPATCHER_SERVLET_BEAN_NAME)
public ServletRegistrationBean dispatcherServletRegistration(
      DispatcherServlet dispatcherServlet) {
   ServletRegistrationBean registration = new ServletRegistrationBean(
         dispatcherServlet, this.serverProperties.getServletMapping());
    //默认拦截： /  所有请求；包静态资源，但是不拦截jsp请求；   /*会拦截jsp
    //可以通过server.servletPath来修改SpringMVC前端控制器默认拦截的请求路径

   registration.setName(DEFAULT_DISPATCHER_SERVLET_BEAN_NAME);
   registration.setLoadOnStartup(
         this.webMvcProperties.getServlet().getLoadOnStartup());
   if (this.multipartConfig != null) {
      registration.setMultipartConfig(this.multipartConfig);
   }
   return registration;
}

```

2）、SpringBoot 能不能支持其他的 Servlet 容器；

### 3）、替换为其他嵌入式 Servlet 容器

![](media/搜狗截图20180302114401.png)

默认支持：

Tomcat（默认使用）

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   引入web模块默认就是使用嵌入式的Tomcat作为Servlet容器；
</dependency>
```

Jetty

```xml
<!-- 引入web模块 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   <exclusions>
      <exclusion>
         <artifactId>spring-boot-starter-tomcat</artifactId>
         <groupId>org.springframework.boot</groupId>
      </exclusion>
   </exclusions>
</dependency>

<!--引入其他的Servlet容器-->
<dependency>
   <artifactId>spring-boot-starter-jetty</artifactId>
   <groupId>org.springframework.boot</groupId>
</dependency>
```

Undertow

```xml
<!-- 引入web模块 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   <exclusions>
      <exclusion>
         <artifactId>spring-boot-starter-tomcat</artifactId>
         <groupId>org.springframework.boot</groupId>
      </exclusion>
   </exclusions>
</dependency>

<!--引入其他的Servlet容器-->
<dependency>
   <artifactId>spring-boot-starter-undertow</artifactId>
   <groupId>org.springframework.boot</groupId>
</dependency>
```

### 4）、嵌入式 Servlet 容器自动配置原理；

EmbeddedServletContainerAutoConfiguration：嵌入式的 Servlet 容器自动配置？

```java
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
@Configuration
@ConditionalOnWebApplication
@Import(BeanPostProcessorsRegistrar.class)
//导入BeanPostProcessorsRegistrar：Spring注解版；给容器中导入一些组件
//导入了EmbeddedServletContainerCustomizerBeanPostProcessor：
//后置处理器：bean初始化前后（创建完对象，还没赋值赋值）执行初始化工作
public class EmbeddedServletContainerAutoConfiguration {

    @Configuration
	@ConditionalOnClass({ Servlet.class, Tomcat.class })//判断当前是否引入了Tomcat依赖；
	@ConditionalOnMissingBean(value = EmbeddedServletContainerFactory.class, search = SearchStrategy.CURRENT)//判断当前容器没有用户自己定义EmbeddedServletContainerFactory：嵌入式的Servlet容器工厂；作用：创建嵌入式的Servlet容器
	public static class EmbeddedTomcat {

		@Bean
		public TomcatEmbeddedServletContainerFactory tomcatEmbeddedServletContainerFactory() {
			return new TomcatEmbeddedServletContainerFactory();
		}

	}

    /**
	 * Nested configuration if Jetty is being used.
	 */
	@Configuration
	@ConditionalOnClass({ Servlet.class, Server.class, Loader.class,
			WebAppContext.class })
	@ConditionalOnMissingBean(value = EmbeddedServletContainerFactory.class, search = SearchStrategy.CURRENT)
	public static class EmbeddedJetty {

		@Bean
		public JettyEmbeddedServletContainerFactory jettyEmbeddedServletContainerFactory() {
			return new JettyEmbeddedServletContainerFactory();
		}

	}

	/**
	 * Nested configuration if Undertow is being used.
	 */
	@Configuration
	@ConditionalOnClass({ Servlet.class, Undertow.class, SslClientAuthMode.class })
	@ConditionalOnMissingBean(value = EmbeddedServletContainerFactory.class, search = SearchStrategy.CURRENT)
	public static class EmbeddedUndertow {

		@Bean
		public UndertowEmbeddedServletContainerFactory undertowEmbeddedServletContainerFactory() {
			return new UndertowEmbeddedServletContainerFactory();
		}

	}
```

1）、EmbeddedServletContainerFactory（嵌入式 Servlet 容器工厂）

```java
public interface EmbeddedServletContainerFactory {

   //获取嵌入式的Servlet容器
   EmbeddedServletContainer getEmbeddedServletContainer(
         ServletContextInitializer... initializers);

}
```

![](media/搜狗截图20180302144835.png)

2）、EmbeddedServletContainer：（嵌入式的 Servlet 容器）

![](media/搜狗截图20180302144910.png)

3）、以**TomcatEmbeddedServletContainerFactory**为例

```java
@Override
public EmbeddedServletContainer getEmbeddedServletContainer(
      ServletContextInitializer... initializers) {
    //创建一个Tomcat
   Tomcat tomcat = new Tomcat();

    //配置Tomcat的基本环节
   File baseDir = (this.baseDirectory != null ? this.baseDirectory
         : createTempDir("tomcat"));
   tomcat.setBaseDir(baseDir.getAbsolutePath());
   Connector connector = new Connector(this.protocol);
   tomcat.getService().addConnector(connector);
   customizeConnector(connector);
   tomcat.setConnector(connector);
   tomcat.getHost().setAutoDeploy(false);
   configureEngine(tomcat.getEngine());
   for (Connector additionalConnector : this.additionalTomcatConnectors) {
      tomcat.getService().addConnector(additionalConnector);
   }
   prepareContext(tomcat.getHost(), initializers);

    //将配置好的Tomcat传入进去，返回一个EmbeddedServletContainer；并且启动Tomcat服务器
   return getTomcatEmbeddedServletContainer(tomcat);
}
```

4）、我们对嵌入式容器的配置修改是怎么生效？

```
ServerProperties、EmbeddedServletContainerCustomizer
```

**EmbeddedServletContainerCustomizer**：定制器帮我们修改了 Servlet 容器的配置？

怎么修改的原理？

5）、容器中导入了**EmbeddedServletContainerCustomizerBeanPostProcessor**

```java
//初始化之前
@Override
public Object postProcessBeforeInitialization(Object bean, String beanName)
      throws BeansException {
    //如果当前初始化的是一个ConfigurableEmbeddedServletContainer类型的组件
   if (bean instanceof ConfigurableEmbeddedServletContainer) {
       //
      postProcessBeforeInitialization((ConfigurableEmbeddedServletContainer) bean);
   }
   return bean;
}

private void postProcessBeforeInitialization(
			ConfigurableEmbeddedServletContainer bean) {
    //获取所有的定制器，调用每一个定制器的customize方法来给Servlet容器进行属性赋值；
    for (EmbeddedServletContainerCustomizer customizer : getCustomizers()) {
        customizer.customize(bean);
    }
}

private Collection<EmbeddedServletContainerCustomizer> getCustomizers() {
    if (this.customizers == null) {
        // Look up does not include the parent context
        this.customizers = new ArrayList<EmbeddedServletContainerCustomizer>(
            this.beanFactory
            //从容器中获取所有这葛类型的组件：EmbeddedServletContainerCustomizer
            //定制Servlet容器，给容器中可以添加一个EmbeddedServletContainerCustomizer类型的组件
            .getBeansOfType(EmbeddedServletContainerCustomizer.class,
                            false, false)
            .values());
        Collections.sort(this.customizers, AnnotationAwareOrderComparator.INSTANCE);
        this.customizers = Collections.unmodifiableList(this.customizers);
    }
    return this.customizers;
}

ServerProperties也是定制器
```

步骤：

1）、SpringBoot 根据导入的依赖情况，给容器中添加相应的 EmbeddedServletContainerFactory【TomcatEmbeddedServletContainerFactory】

2）、容器中某个组件要创建对象就会惊动后置处理器；EmbeddedServletContainerCustomizerBeanPostProcessor；

只要是嵌入式的 Servlet 容器工厂，后置处理器就工作；

3）、后置处理器，从容器中获取所有的**EmbeddedServletContainerCustomizer**，调用定制器的定制方法

###5）、嵌入式 Servlet 容器启动原理；

什么时候创建嵌入式的 Servlet 容器工厂？什么时候获取嵌入式的 Servlet 容器并启动 Tomcat；

获取嵌入式的 Servlet 容器工厂：

1）、SpringBoot 应用启动运行 run 方法

2）、refreshContext(context);SpringBoot 刷新 IOC 容器【创建 IOC 容器对象，并初始化容器，创建容器中的每一个组件】；如果是 web 应用创建**AnnotationConfigEmbeddedWebApplicationContext**，否则：**AnnotationConfigApplicationContext**

3）、refresh(context);**刷新刚才创建好的 ioc 容器；**

```java
public void refresh() throws BeansException, IllegalStateException {
   synchronized (this.startupShutdownMonitor) {
      // Prepare this context for refreshing.
      prepareRefresh();

      // Tell the subclass to refresh the internal bean factory.
      ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

      // Prepare the bean factory for use in this context.
      prepareBeanFactory(beanFactory);

      try {
         // Allows post-processing of the bean factory in context subclasses.
         postProcessBeanFactory(beanFactory);

         // Invoke factory processors registered as beans in the context.
         invokeBeanFactoryPostProcessors(beanFactory);

         // Register bean processors that intercept bean creation.
         registerBeanPostProcessors(beanFactory);

         // Initialize message source for this context.
         initMessageSource();

         // Initialize event multicaster for this context.
         initApplicationEventMulticaster();

         // Initialize other special beans in specific context subclasses.
         onRefresh();

         // Check for listener beans and register them.
         registerListeners();

         // Instantiate all remaining (non-lazy-init) singletons.
         finishBeanFactoryInitialization(beanFactory);

         // Last step: publish corresponding event.
         finishRefresh();
      }

      catch (BeansException ex) {
         if (logger.isWarnEnabled()) {
            logger.warn("Exception encountered during context initialization - " +
                  "cancelling refresh attempt: " + ex);
         }

         // Destroy already created singletons to avoid dangling resources.
         destroyBeans();

         // Reset 'active' flag.
         cancelRefresh(ex);

         // Propagate exception to caller.
         throw ex;
      }

      finally {
         // Reset common introspection caches in Spring's core, since we
         // might not ever need metadata for singleton beans anymore...
         resetCommonCaches();
      }
   }
}
```

4）、 onRefresh(); web 的 ioc 容器重写了 onRefresh 方法

5）、webioc 容器会创建嵌入式的 Servlet 容器；**createEmbeddedServletContainer**();

**6）、获取嵌入式的 Servlet 容器工厂：**

EmbeddedServletContainerFactory containerFactory = getEmbeddedServletContainerFactory();

从 ioc 容器中获取 EmbeddedServletContainerFactory 组件；**TomcatEmbeddedServletContainerFactory**创建对象，后置处理器一看是这个对象，就获取所有的定制器来先定制 Servlet 容器的相关配置；

7）、**使用容器工厂获取嵌入式的 Servlet 容器**：this.embeddedServletContainer = containerFactory .getEmbeddedServletContainer(getSelfInitializer());

8）、嵌入式的 Servlet 容器创建对象并启动 Servlet 容器；

**先启动嵌入式的 Servlet 容器，再将 ioc 容器中剩下没有创建出的对象获取出来；**

**==IOC 容器启动创建嵌入式的 Servlet 容器==**

## 9、使用外置的 Servlet 容器

嵌入式 Servlet 容器：应用打成可执行的 jar

优点：简单、便携；

缺点：默认不支持 JSP、优化定制比较复杂（使用定制器【ServerProperties、自定义 EmbeddedServletContainerCustomizer】，自己编写嵌入式 Servlet 容器的创建工厂【EmbeddedServletContainerFactory】）；

外置的 Servlet 容器：外面安装 Tomcat---应用 war 包的方式打包；

### 步骤

1）、必须创建一个 war 项目；（利用 idea 创建好目录结构）

2）、将嵌入式的 Tomcat 指定为 provided；

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-tomcat</artifactId>
   <scope>provided</scope>
</dependency>
```

3）、必须编写一个**SpringBootServletInitializer**的子类，并调用 configure 方法

```java
public class ServletInitializer extends SpringBootServletInitializer {

   @Override
   protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
       //传入SpringBoot应用的主程序
      return application.sources(SpringBoot04WebJspApplication.class);
   }

}
```

4）、启动服务器就可以使用；

### 原理

jar 包：执行 SpringBoot 主类的 main 方法，启动 ioc 容器，创建嵌入式的 Servlet 容器；

war 包：启动服务器，**服务器启动 SpringBoot 应用**【SpringBootServletInitializer】，启动 ioc 容器；

servlet3.0（Spring 注解版）：

规则：8.2.4 Shared libraries / runtimes pluggability：

1）、服务器启动（web 应用启动）会创建当前 web 应用里面每一个 jar 包里面 ServletContainerInitializer 实例：

2）、ServletContainerInitializer 的实现放在 jar 包的 META-INF/services 文件夹下，有一个名为 javax.servlet.ServletContainerInitializer 的文件，内容就是 ServletContainerInitializer 的实现类的全类名

3）、还可以使用@HandlesTypes，在应用启动的时候加载我们感兴趣的类；

流程：

1）、启动 Tomcat

2）、服务器启动（web 应用启动）会创建当前 web 应用里面每一个 jar 包里面 ServletContainerInitializer 实例：Spring 的 web 模块里面有这个文件：**org.springframework.web.SpringServletContainerInitializer**

3）、SpringServletContainerInitializer 将@HandlesTypes(WebApplicationInitializer.class)标注的所有这个类型的类都传入到 onStartup 方法的 Set<Class<?>>；为这些 WebApplicationInitializer 类型的类创建实例；

4）、每一个 WebApplicationInitializer 都调用自己的 onStartup；它的实现只有下边三个

![](media/搜狗截图20180302221835.png)

5）、相当于我们的 SpringBootServletInitializer 的类会被创建对象，并执行 onStartup 方法

6）、SpringBootServletInitializer 实例执行 onStartup 的时候会 createRootApplicationContext；创建容器

7）、Spring 的应用就启动并且创建 IOC 容器

**==启动 Servlet 容器，再启动 SpringBoot 应用==**

# 六、SpringBoot 与数据访问

## 1、JDBC

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jdbc</artifactId>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
```

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://192.168.15.22:3306/jdbc
    driver-class-name: com.mysql.jdbc.Driver
```

效果：

默认是用 org.apache.tomcat.jdbc.pool.DataSource 作为数据源；

数据源的相关配置都在 DataSourceProperties 里面；

自动配置原理：

org.springframework.boot.autoconfigure.jdbc：

1、参考 DataSourceConfiguration，根据配置创建数据源，默认使用 Tomcat 连接池；可以使用 spring.datasource.type 指定自定义的数据源类型；

2、SpringBoot 默认可以支持；

```
org.apache.tomcat.jdbc.pool.DataSource、HikariDataSource、BasicDataSource、
```

3、自定义数据源类型

```java
/**
 * Generic DataSource configuration.
 */
@ConditionalOnMissingBean(DataSource.class)
@ConditionalOnProperty(name = "spring.datasource.type")
static class Generic {

   @Bean
   public DataSource dataSource(DataSourceProperties properties) {
       //使用DataSourceBuilder创建数据源，利用反射创建响应type的数据源，并且绑定相关属性
      return properties.initializeDataSourceBuilder().build();
   }

}
```

4、**DataSourceInitializer：ApplicationListener**；

作用：

1）、runSchemaScripts();运行建表语句；

2）、runDataScripts();运行插入数据的 sql 语句；

默认只需要将文件命名为：

```properties
schema-*.sql、data-*.sql
默认规则：schema.sql，schema-all.sql；
可以使用
	schema:
      - classpath:department.sql
      指定位置
```

5、操作数据库：自动配置了 JdbcTemplate 操作数据库

## 2、整合 Druid 数据源

```java
导入druid数据源
@Configuration
public class DruidConfig {

    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druid(){
       return  new DruidDataSource();
    }

    //配置Druid的监控
    //1、配置一个管理后台的Servlet
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
        Map<String,String> initParams = new HashMap<>();

        initParams.put("loginUsername","admin");
        initParams.put("loginPassword","123456");
        initParams.put("allow","");//默认就是允许所有访问
        initParams.put("deny","192.168.15.21");

        bean.setInitParameters(initParams);
        return bean;
    }


    //2、配置一个web监控的filter
    @Bean
    public FilterRegistrationBean webStatFilter(){
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());

        Map<String,String> initParams = new HashMap<>();
        initParams.put("exclusions","*.js,*.css,/druid/*");

        bean.setInitParameters(initParams);

        bean.setUrlPatterns(Arrays.asList("/*"));

        return  bean;
    }
}

```

## 3、整合 MyBatis

```xml
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.1</version>
		</dependency>
```

![](media/搜狗截图20180305194443.png)

步骤：

1）、配置数据源相关属性（见上一节 Druid）

2）、给数据库建表

3）、创建 JavaBean

### 4）、注解版

```java
//指定这是一个操作数据库的mapper
@Mapper
public interface DepartmentMapper {

    @Select("select * from department where id=#{id}")
    public Department getDeptById(Integer id);

    @Delete("delete from department where id=#{id}")
    public int deleteDeptById(Integer id);

    @Options(useGeneratedKeys = true,keyProperty = "id")
    @Insert("insert into department(departmentName) values(#{departmentName})")
    public int insertDept(Department department);

    @Update("update department set departmentName=#{departmentName} where id=#{id}")
    public int updateDept(Department department);
}
```

问题：

自定义 MyBatis 的配置规则；给容器中添加一个 ConfigurationCustomizer；

```java
@org.springframework.context.annotation.Configuration
public class MyBatisConfig {

    @Bean
    public ConfigurationCustomizer configurationCustomizer(){
        return new ConfigurationCustomizer(){

            @Override
            public void customize(Configuration configuration) {
                configuration.setMapUnderscoreToCamelCase(true);
            }
        };
    }
}
```

```java
使用MapperScan批量扫描所有的Mapper接口；
@MapperScan(value = "com.xqc.springboot.mapper")
@SpringBootApplication
public class SpringBoot06DataMybatisApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBoot06DataMybatisApplication.class, args);
	}
}
```

### 5）、配置文件版

```yaml
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml 指定全局配置文件的位置
  mapper-locations: classpath:mybatis/mapper/*.xml  指定sql映射文件的位置
```

更多使用参照

http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/

## 4、整合 SpringData JPA

### 1）、SpringData 简介

![](media/搜狗截图20180306105412.png)

### 2）、整合 SpringData JPA

JPA:ORM（Object Relational Mapping）；

1）、编写一个实体类（bean）和数据表进行映射，并且配置好映射关系；

```java
//使用JPA注解配置映射关系
@Entity //告诉JPA这是一个实体类（和数据表映射的类）
@Table(name = "tbl_user") //@Table来指定和哪个数据表对应;如果省略默认表名就是user；
public class User {

    @Id //这是一个主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)//自增主键
    private Integer id;

    @Column(name = "last_name",length = 50) //这是和数据表对应的一个列
    private String lastName;
    @Column //省略默认列名就是属性名
    private String email;
```

2）、编写一个 Dao 接口来操作实体类对应的数据表（Repository）

```java
//继承JpaRepository来完成对数据库的操作
public interface UserRepository extends JpaRepository<User,Integer> {
}

```

3）、基本的配置 JpaProperties

```yaml
spring:
  jpa:
    hibernate:
      #     更新或者创建数据表结构
      ddl-auto: update
    #    控制台显示SQL
    show-sql: true
```

# 七、启动配置原理

几个重要的事件回调机制

配置在 META-INF/spring.factories

**ApplicationContextInitializer**

**SpringApplicationRunListener**

只需要放在 ioc 容器中

**ApplicationRunner**

**CommandLineRunner**

启动流程：

## **1、创建 SpringApplication 对象**

```java
initialize(sources);
private void initialize(Object[] sources) {
    //保存主配置类
    if (sources != null && sources.length > 0) {
        this.sources.addAll(Arrays.asList(sources));
    }
    //判断当前是否一个web应用
    this.webEnvironment = deduceWebEnvironment();
    //从类路径下找到META-INF/spring.factories配置的所有ApplicationContextInitializer；然后保存起来
    setInitializers((Collection) getSpringFactoriesInstances(
        ApplicationContextInitializer.class));
    //从类路径下找到ETA-INF/spring.factories配置的所有ApplicationListener
    setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
    //从多个配置类中找到有main方法的主配置类
    this.mainApplicationClass = deduceMainApplicationClass();
}
```

![](media/搜狗截图20180306145727.png)

![](media/搜狗截图20180306145855.png)

## 2、运行 run 方法

```java
public ConfigurableApplicationContext run(String... args) {
   StopWatch stopWatch = new StopWatch();
   stopWatch.start();
   ConfigurableApplicationContext context = null;
   FailureAnalyzers analyzers = null;
   configureHeadlessProperty();

   //获取SpringApplicationRunListeners；从类路径下META-INF/spring.factories
   SpringApplicationRunListeners listeners = getRunListeners(args);
    //回调所有的获取SpringApplicationRunListener.starting()方法
   listeners.starting();
   try {
       //封装命令行参数
      ApplicationArguments applicationArguments = new DefaultApplicationArguments(
            args);
      //准备环境
      ConfigurableEnvironment environment = prepareEnvironment(listeners,
            applicationArguments);
       		//创建环境完成后回调SpringApplicationRunListener.environmentPrepared()；表示环境准备完成

      Banner printedBanner = printBanner(environment);

       //创建ApplicationContext；决定创建web的ioc还是普通的ioc
      context = createApplicationContext();

      analyzers = new FailureAnalyzers(context);
       //准备上下文环境;将environment保存到ioc中；而且applyInitializers()；
       //applyInitializers()：回调之前保存的所有的ApplicationContextInitializer的initialize方法
       //回调所有的SpringApplicationRunListener的contextPrepared()；
       //
      prepareContext(context, environment, listeners, applicationArguments,
            printedBanner);
       //prepareContext运行完成以后回调所有的SpringApplicationRunListener的contextLoaded（）；

       //s刷新容器；ioc容器初始化（如果是web应用还会创建嵌入式的Tomcat）；Spring注解版
       //扫描，创建，加载所有组件的地方；（配置类，组件，自动配置）
      refreshContext(context);
       //从ioc容器中获取所有的ApplicationRunner和CommandLineRunner进行回调
       //ApplicationRunner先回调，CommandLineRunner再回调
      afterRefresh(context, applicationArguments);
       //所有的SpringApplicationRunListener回调finished方法
      listeners.finished(context, null);
      stopWatch.stop();
      if (this.logStartupInfo) {
         new StartupInfoLogger(this.mainApplicationClass)
               .logStarted(getApplicationLog(), stopWatch);
      }
       //整个SpringBoot应用启动完成以后返回启动的ioc容器；
      return context;
   }
   catch (Throwable ex) {
      handleRunFailure(context, listeners, analyzers, ex);
      throw new IllegalStateException(ex);
   }
}
```

## 3、事件监听机制

配置在 META-INF/spring.factories

**ApplicationContextInitializer**

```java
public class HelloApplicationContextInitializer implements ApplicationContextInitializer<ConfigurableApplicationContext> {
    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        System.out.println("ApplicationContextInitializer...initialize..."+applicationContext);
    }
}

```

**SpringApplicationRunListener**

```java
public class HelloSpringApplicationRunListener implements SpringApplicationRunListener {

    //必须有的构造器
    public HelloSpringApplicationRunListener(SpringApplication application, String[] args){

    }

    @Override
    public void starting() {
        System.out.println("SpringApplicationRunListener...starting...");
    }

    @Override
    public void environmentPrepared(ConfigurableEnvironment environment) {
        Object o = environment.getSystemProperties().get("os.name");
        System.out.println("SpringApplicationRunListener...environmentPrepared.."+o);
    }

    @Override
    public void contextPrepared(ConfigurableApplicationContext context) {
        System.out.println("SpringApplicationRunListener...contextPrepared...");
    }

    @Override
    public void contextLoaded(ConfigurableApplicationContext context) {
        System.out.println("SpringApplicationRunListener...contextLoaded...");
    }

    @Override
    public void finished(ConfigurableApplicationContext context, Throwable exception) {
        System.out.println("SpringApplicationRunListener...finished...");
    }
}

```

配置（META-INF/spring.factories）

```properties
org.springframework.context.ApplicationContextInitializer=\
com.xqc.springboot.listener.HelloApplicationContextInitializer

org.springframework.boot.SpringApplicationRunListener=\
com.xqc.springboot.listener.HelloSpringApplicationRunListener
```

只需要放在 ioc 容器中

**ApplicationRunner**

```java
@Component
public class HelloApplicationRunner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("ApplicationRunner...run....");
    }
}
```

**CommandLineRunner**

```java
@Component
public class HelloCommandLineRunner implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("CommandLineRunner...run..."+ Arrays.asList(args));
    }
}
```

# 监视器 Actuator

是 spring 启动框架中的重要功能之一。Spring boot 监视器可帮助您访问生产环境中正在运行的应用程序的当前状态。有几个指标必须在生产环境中进行检查和监控。即使一些外部应用程序可能正在使用这些服务来向相关人员触发警报消息。监视器模块公开了一组可直接作为 HTTP URL 访问的 REST 端点来检查状态。

## SpringBoot 打成 jar 和普通的 jar 有什么区别？

Spring Boot 项目最终打包成的 jar 是可执行 jar ，这种 jar 可以直接通过`java -jar xxx.jar`命令来运行，这种 jar 不可以作为普通的 jar 被其他项目依赖，即使依赖了也无法使用其中的类。

Spring Boot 的 jar 无法被其他项目依赖，主要还是他和普通 jar 的结构不同。普通的 jar 包，解压后直接就是包名，包里就是我们的代码，而 Spring Boot 打包成的可执行 jar 解压后，在 `\BOOT-INF\classes`目录下才是我们的代码，因此无法被直接引用。如果非要引用，可以在 pom.xml 文件中增加配置，将 Spring Boot 项目打包成两个 jar ，一个可执行，一个可引用。

## 跨域问题：

跨域可以在前端通过 JSONP 来解决，但是 JSONP 只可以发送 GET 请求，无法发送其他类型的请求，在 RESTful 风格的应用中，就显得非常鸡肋，因此推荐在后端通过（CORS，Cross-origin resource sharing）来解决跨域问题。这种解决方案并非 Spring Boot 特有的，在传统的 SSM 框架中，就可以通过 CORS 来解决跨域问题，只不过之前我们是在 XML 文件中配置 CORS，现在可以通过实现 WebMvcConfigurer 接口然后重写 addCorsMappings 方法解决跨域问题。

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowCredentials(true)
                .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                .maxAge(3600);
    }
}
```

项目中前后端分离部署，所以需要解决跨域的问题。

# 十：安全

CSRF 代表跨站请求伪造。这是一种攻击，迫使最终用户在当前通过身份验证的 Web 应用程序上执行不需要的操作。CSRF 攻击专门针对状态改变请求，而不是数据窃取，因为攻击者无法查看对伪造请求的响应。
