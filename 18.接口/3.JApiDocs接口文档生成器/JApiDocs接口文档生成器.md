## 一、添加依赖

```xml
<dependency>
  <groupId>io.github.yedaxia</groupId>
  <artifactId>japidocs</artifactId>
  <version>1.3</version>
</dependency>
```

## 二、配置生成参数

我们新建一个项目，然后随便写一个main方法，增加生成文档的配置，然后运行main方法。

```java
DocsConfig config = new DocsConfig();
config.setProjectPath("F:\\Java旅途\\japi-docs"); // 项目根目录
config.setProjectName("japi-docs"); // 项目名称
config.setApiVersion("V1.0");       // 声明该API的版本
config.setDocsPath("F:\\test"); // 生成API 文档所在目录
config.setAutoGenerate(Boolean.TRUE);  // 配置自动生成
Docs.buildHtmlDocs(config); // 执行生成文档
```

## 三、编码规范

由于JApiDocs是通过解析Java源码来实现的，因此如果要想实现想要的文档，还是需要遵循一定的规范。

### 3.1 类注释、方法注释和属性注释

如果我们想生成类的注释，我们可以直接在类上加注释，也可以通过加@description来生成。

```java
/**
 * 用户接口类
 */
@RequestMapping("/api/user")
@RestController
public class UserController {}

/**
 * @author Java旅途
 * @Description 用户接口类
 * @Date 2020-06-15 21:46
 */
@RequestMapping("/api/user")
@RestController
public class UserController {}
```

如果我们想生成方法的注释，只能直接加注释，不能通过加@description来生成。

```java
/**
 * 查询用户
 * @param age 年龄
 * @return R<User>
*/
@GetMapping("/list")
public R<User> list(@RequestParam int age){
    User user = new User("Java旅途", 18);
    return R.ok(user);
}
```

JApiDocs可以自动生成实体类，关于实体类属性的注释有三种方式，生成的效果都是一样的，如下：

```
/**
 * 用户名称
 */
private String name;
/**
 * 用户年龄
 */
private int age;
// 用户名称
private String name;
// 用户年龄
private int age;
private String name;// 用户名称
private int age;// 用户年龄
```

他除了支持咱们常用的model外，还支持IOS的model生成效果

### 3.2 请求参数

如果提交的表单是 `application/x-www-form-urlencoded` 类型的`key/value`格式，则我们通过@param注解来获取参数，在参数后面添加注释，示例如下：

```
/**
  * @param age 年龄
  */
@GetMapping("/list")
public R<User> list(@RequestParam int age){
    User user = new User("Java旅途", 18);
    return R.ok(user);
}
```

生成的文档效果如下：

请求参数

| 参数名 | 类型 | 必须 | 描述 |
| :----- | :--- | :--- | :--- |
| age    | int  | 否   | 年龄 |

如果提交的表单是 `application/json` 类型的`json`数据格式，如下：

```
/**
  * @param user
  * @return
  */
@PostMapping("/add")
public R<User> add(@RequestBody User user){
    return R.ok(user);
}
```

生成的文档效果如下：

请求参数

```
{
  "name": "string //用户名称",
  "age": "int //用户年龄"
}
```

### 3.3 响应结果

> 我们知道，如果`Controller`声明了`@RestController`，SpringBoot会把返回的对象直接序列成Json数据格式返回给前端。JApiDocs也利用了这一特性来解析接口返回的结果，但由于JApiDocs是静态解析源码的，因此你要明确指出返回对象的类型信息，JApiDocs支持继承、泛型、循环嵌套等复杂的类解析。

因此我们不需要再写注释，它会根据我们的返回结果进行解析，效果如下：

返回结果：

```
{
  "code": "int",
  "msg": "string",
  "data": {
    "name": "string //用户名称",
    "age": "int //用户年龄"
  }
}
```

最终，我们生成的接口文档，如下：

![图片](media/640.png)

## 四、高级配置 

### 4.1 @ApiDoc

如果你不希望把所有的接口都导出，我们可以在配置中设置config.setAutoGenerate(Boolean.FALSE);然后在想要生成的接口上添加@ApiDoc。

@ApiDoc有以下三个属性：

- result: 这个可以直接声明返回的对象类型，如果你声明了，将会覆盖SpringBoot的返回对象
- url: 请求URL，扩展字段，用于支持非SpringBoot项目
- method: 请求方法，扩展字段，用于支持非SpringBoot项目

```java
@ApiDoc(result = User.class, url = "/api/user/view", method = "post")
```

### 4.2 @Ignore

如果你不想导出对象里面的某个字段，可以给这个字段加上`@Ignore`注解，这样JApiDocs导出文档的时候就会自动忽略掉了。

```java
public class User {
    @Ignore
    private int age;
}
```



但是JApiDocs不具备Swagger在线调试功能。