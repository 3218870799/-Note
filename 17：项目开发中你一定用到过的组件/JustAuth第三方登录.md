官方文档：https://justauth.wiki/quickstart/explain.html



## 使用步骤：

使用JustAuth总共分三步（**这三步也适合于JustAuth支持的任何一个平台**）：

1. 申请注册第三方平台的开发者账号
2. 创建第三方平台的应用，获取配置信息(`accessKey`, `secretKey`, `redirectUri`)
3. 使用该工具实现授权登陆

## 使用方式

引入依赖：

```xml
<dependency>
    <groupId>me.zhyd.oauth</groupId>
    <artifactId>JustAuth</artifactId>
    <version>${latest.version}</version>
</dependency>
```

调用API

```java
// 创建授权request
AuthRequest authRequest = new AuthGiteeRequest(AuthConfig.builder()
        .clientId("clientId")
        .clientSecret("clientSecret")
        .redirectUri("redirectUri")
        .build());
// 生成授权页面
authRequest.authorize("state");
// 授权登录后会返回code（auth_code（仅限支付宝））、state，1.8.0版本后，可以用AuthCallback类作为回调接口的参数
// 注：JustAuth默认保存state的时效为3分钟，3分钟内未使用则会自动清除过期的state
//登录获取用户信息
AuthResponse response = authRequest.login(callback);
```

