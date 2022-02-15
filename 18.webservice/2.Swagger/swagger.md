# 一：Swagger

你还在为编写高质量API而苦恼吗？你还在为随时更新而烦躁吗？你还在为专门的测试工具而安装软件吗？Swagger2来帮你！！

你只需要添加必要的注解，Swagger2会自动生成Restful 风格的API文档。并且及时更新，代码变化，文档也随之变化。

提供了统一的测试环境，方便调试

更好的书写API文档的规范且完整框架。

提供描述、生产、消费和可视化RESTful Web Service。

# 二：使用

1：添加依赖：

```xml
<!--swagger2的jar包-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<!--引入视觉的样式的UI-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

第一个是API获取的包，第二是官方给出的一个ui界面。这个界面可以自定义，默认是官方的，对于安全问题，以及ui路由设置需要着重思考。

2、swagger的configuration

需要特别注意的是swagger scan base package,这是扫描注解的配置，即你的API接口位置。

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class Swagger2 {

        @Bean
        public Docket createRestApi() {
            return new Docket(DocumentationType.SWAGGER_2)
                    .apiInfo(apiInfo())
                    .select()
                    .apis(RequestHandlerSelectors.basePackage("com.yss.ms.admin"))
                    .paths(PathSelectors.any())
                    .build();
        }

        private ApiInfo apiInfo() {
            return new ApiInfoBuilder()
                    .title("服务:发布为daocke镜像,权限管理，用户管理，页面管理，日志 后台 APIs")
                    .description("服务:发布为daocke镜像,权限管理，用户管理，页面管理，日志 后台")
                    .termsOfServiceUrl("http://192.168.1.198:10070/platformgroup/ms-admin")
                    .contact("程序猿")
                    .version("1.0")
                    .build();
        }

    }
```

3：在Controller层添加必要的注解

```java
@Api(value = "API - VehiclesController", description = "车辆模块接口详情")
@RestController
@RequestMapping("/vehicles")
public class VehiclesController {

    private static Logger logger = LoggerFactory.getLogger(VehiclesController.class);


    @ApiOperation(value = "查询车辆接口", notes = "此接口描述xxxxxxxxxxxxx<br/>xxxxxxx<br>值得庆幸的是这儿支持html标签<hr/>", response = String.class)
    @ApiImplicitParams({
            @ApiImplicitParam(name = "vno", value = "车牌", required = false,
                    dataType = "string", paramType = "query", defaultValue = "辽A12345"),
            @ApiImplicitParam(name = "page", value = "page", required = false,
                    dataType = "Integer", paramType = "query",defaultValue = "1"),
            @ApiImplicitParam(name = "count", value = "count", required = false,
                    dataType = "Integer", paramType = "query",defaultValue = "10")
    })
    @ApiResponses(value = {
            @ApiResponse(code = 200, message = "Successful — 请求已完成"),
            @ApiResponse(code = 400, message = "请求中有语法问题，或不能满足请求"),
            @ApiResponse(code = 401, message = "未授权客户机访问数据"),
            @ApiResponse(code = 404, message = "服务器找不到给定的资源；文档不存在"),
            @ApiResponse(code = 500, message = "服务器不能完成请求")}
    )
    @ResponseBody
    @RequestMapping(value = "", method = RequestMethod.GET)
    public ModelMap findVehicles(@RequestParam(value = "vno", required = false) String vno,
                                 @RequestParam(value = "page", required = false) Integer page,
                                 @RequestParam(value = "count", required = false) Integer count)
            throws Exception {

        logger.info("http://localhost:8501/api/v1/vehicles");
        logger.info("## {},{},{}", vno, page, count);
        logger.info("## 请求时间：{}", new Date());

        ModelMap map = new ModelMap();
        map.addAttribute("vno", vno);
        map.addAttribute("page", page);
        return map;
    }
}
```

4：查看

http://localhost:8080/api/v1/swagger-ui.html

## 常用API

1、api标记

Api 用在类上，说明该类的作用。可以标记一个Controller类做为swagger 文档资源，使用方式：

```java
@Api(value = "/user", description = "Operations about user")
@RestController
@RequestMappering("/user")
public class UserController{
    
}
```

2、ApiOperation标记

ApiOperation：用在方法上，说明方法的作用，每一个url资源的定义,使用方式：

```java
@ApiOperation(
          value = "Find purchase order by ID",
          notes = "For valid response try integer IDs with value <= 5 or > 10. Other values will generated exceptions",
          response = Order,
          tags = {"Pet Store"})
```

3、ApiParam标记

ApiParam请求属性,使用方式：

```java
public ResponseEntity<User> createUser(@RequestBody @ApiParam(value = "Created user object", required = true)  User user)
```

4、ApiResponse

ApiResponse：响应配置，使用方式：

```java
@ApiResponse(code = 400, message = "Invalid user supplied")
```

5、ApiResponses

ApiResponses：响应集配置，使用方式：

```java
@ApiResponses({ @ApiResponse(code = 400, message = "Invalid Order") })
```

6、ResponseHeader

响应头设置，使用方法

```java
@ResponseHeader(name="head1",description="response head conf")
```
7：@ApiModel：用于类，表示对类的说明，参数用实体类接受；

8：@ApiIgnore：用于类，方法，参数
表示这个方法或类被忽略；



| 注解                 | 属性         | 值                      | 备注                                                         |
| -------------------- | ------------ | ----------------------- | ------------------------------------------------------------ |
| @Api                 | value        | 字符串                  | 可用在`class`头上,`class`描述                                |
|                      | description  | 字符串                  |                                                              |
|                      |              |                         | `@Api`(value = "xxx", description = "xxx")                   |
| @ApiOperation        | value        | 字符串                  | 可用在方法头上.参数的描述容器                                |
|                      | notes        | 字符串                  | 方法的备用说明                                               |
|                      |              |                         | `@ApiOperation`(value = "xxx", notes = "xxx")                |
| `@ApiImplicitParams` | {}           | `@ApiImplicitParam`数组 | 可用在方法头上.参数的描述容器                                |
|                      |              |                         | `@ApiImplicitParams`({`@ApiImplicitParam1`,`@ApiImplicitParam2`,...}) |
| `@ApiImplicitParam`  | name         | 字符串 与参数命名对应   | 可用在`@ApiImplicitParams`里                                 |
|                      | value        | 字符串                  | 参数中文描述                                                 |
|                      | required     | 布尔值                  | true/false                                                   |
|                      | dataType     | 字符串                  | 参数类型                                                     |
|                      | paramType    | 字符串                  | 参数请求方式:query/path                                      |
|                      |              |                         | query:对应`@RequestParam`?传递                               |
|                      |              |                         | path: 对应`@PathVariable`{}path传递                          |
|                      | defaultValue | 字符串                  | 在api测试中默认值                                            |
|                      |              |                         | 用例参见项目中的设置                                         |
| @ApiParam            |              |                         | 单个参数描述                                                 |
| @ApiModel            |              |                         | 用对象实体作为入参，实体类上使用                             |
| @ApiModelProperty    |              |                         | 使用在被@ApiModel注解的模型类的属性上                        |
| @ApiProperty         |              |                         | 用对象接收实体参数时，描述对象的一个字段                     |
| `@ApiResponses`      | {}           | `@ApiResponse`数组      | 可用在方法头上.参数的描述容器，HTTP相应整体描述              |
|                      | code         | 数字                    | `例如400,404`                                                |
|                      | message      | 信息                    | `例如"请求参数没填好"`                                       |
|                      | response     |                         | `抛出异常的类`                                               |
| @ApiResponse         |              |                         | `HTTP响应其中1个描述，方法返回值的说明`                      |
| @ApiIgnore           |              |                         | `使用注解忽略这个API`                                        |
| @ApiError            |              |                         | `发生错误返回的信息`                                         |
| @ApiImplicitParam    |              |                         | `一个请求参数`                                               |
| @ApiImplicitParams   |              |                         | `多个请求参数`                                               |
|                      | name         |                         | `参数名`                                                     |
|                      | value        |                         | `参数的汉字说明，解释`                                       |
|                      | required     |                         | `参数是否必须传`                                             |
|                      | paramType    |                         | `参数放在哪个地方``· header --> 请求参数的获取：@RequestHeader``· query --> 请求参数的获取：@RequestParam``      · path（用于restful接口）--> 请求参数的获取：@PathVariable``      · body（请求体）--> @RequestBody User user``      · form（普通表单提交）` |
|                      | dataType     |                         | 参数类型，默认String，其他值dataType=“int”                   |
|                      | defaultValue |                         | 参数的默认值                                                 |
| @ApiResponse         | code         | 整形                    |                                                              |

