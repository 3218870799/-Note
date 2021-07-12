- JUnit5中支持lambda表达式，语法简单且代码不冗余。
- JUnit5易扩展，包容性强，可以接入其他的测试引擎。
- 功能更强大提供了新的断言机制、参数化测试、重复性测试等新功能。

## 原则

1：快速运行

· 确定什么正常工作；

· 检测什么停止了工作

2：小而专注

3：**100%可靠**

测试是你的安全带，要想100%发挥测试的威力，那就要120%的认真对待测试，保证测试自己是完全可靠的。

最浪费时间和令人泄气的事情，莫过于出现了一个bug，所有的测试还都运行通过了。

## 使用

引入依赖

以SpringBoot项目为例

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

常用注解

## 常用注解

- @BeforeEach：在每个单元测试方法执行前都执行一遍
- @BeforeAll：在每个单元测试方法执行前执行一遍（只执行一次）
- @DisplayName("商品入库测试")：用于指定单元测试的名称
- @Disabled：当前单元测试置为无效，即单元测试时跳过该测试
- @RepeatedTest(n)：重复性测试，即执行n次
- @ParameterizedTest：参数化测试，
- @ValueSource(ints = {1, 2, 3})：参数化测试提供数据

一个Junit的单元测试用例执行顺序

@BeforeClass ——> @Before ——>@Test ——> @After ——> @AfterClass

每一个测试方法的调用顺序

@Before ——> @Test ——> @After

例：

参数化测试

参数化测试可以按照多个参数分别运行多次单元测试这里有点类似于重复性测试，只不过每次运行传入的参数不用。需

要使用到`@ParameterizedTest`，同时也需要`@ValueSource`提供一组数据，它支持八种基本类型以及[`String`](http://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==&mid=2247516217&idx=3&sn=503a783cbfcd939966ab64c312d34bed&chksm=eb50050fdc278c19e8fa759d342ddaf1174a0050b5e21497eddea7155e0981c1952685edf03b&scene=21#wechat_redirect)和自定义对象类型，使用极其方便。

```java
@ParameterizedTest
@ValueSource(ints = {1, 2, 3})
@DisplayName("参数化测试")
void paramTest(int a) {
    assertTrue(a > 0 && a < 4);
}
```

## 静态变量问题

解决：

将每个测试用例都进行类隔离



## Mock代码与桩代码

