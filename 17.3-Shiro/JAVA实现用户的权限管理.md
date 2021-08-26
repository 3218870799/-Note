## 一：权限管理简介

  做系统时肯定遇到最常见的就是不同的用户的需求是不一样的，就拿登陆来说，一个办公管理系统，不同部门的人肯定要求的功能和权限都是不一样的，那你不可能对每一个部门都写一个登陆页面，给不同的url吧！亦或者在下边选择你是什么部门的人？那每个部门内还有等级呐！再继续选？然后给每个人写一个界面？那明显是不可能的，那我们到底要怎么实现呐？

  最常用的方法就是划分不同的角色，赋予角色权限，然后再让用户去申请角色就好了，具体的实现慢慢说。

## 二：数据表的设计

根据角色授权的思想，我们需要涉及五张表(简单一写，没写约束，凑活看吧)

1）三张主表

a）用户表（user）

b） 角色表（role）

c） 资源表（module）[你也可以叫他权限表等等，反正就是代表着各种权限]

2）两张中间表

d）用户角色表（user_role）

e）角色资源表（permission)

```sql
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `username` varchar(32) DEFAULT NULL COMMENT '用户名',
  `password` varchar(32) DEFAULT NULL COMMENT '密码',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户表';

CREATE TABLE `role` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
   `name` varchar(32) DEFAULT NULL COMMENT '角色名',
   `desc` varchar(32) DEFAULT NULL COMMENT '角色描述',
   PRIMARY KEY (`id`)
 ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='角色表';

 CREATE TABLE `resource` (
   `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'id',
   `title` varchar(32) DEFAULT NULL COMMENT '资源标题',
   `uri` varchar(32) DEFAULT NULL COMMENT '资源uri  ',
   PRIMARY KEY (`id`)
 ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='资源表';

 CREATE TABLE `user_role` (
   `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'id',
   `user_id` int(11) NOT NULL COMMENT '用户id',
   `role_id` int(11) NOT NULL COMMENT '角色id',
   PRIMARY KEY (`id`)
 ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户角色表';

 CREATE TABLE `role_resource` (
   `id` int(11) NOT NULL AUTO_INCREMENT,
   `role_id` int(11) DEFAULT NULL COMMENT '角色id',
   `resource_id` int(11) DEFAULT NULL COMMENT '资源id',
   PRIMARY KEY (`id`)
 ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='角色资源表';
```

![img](https://images2018.cnblogs.com/blog/1174906/201808/1174906-20180812190107781-59326520.png)

## 三：使用Shiro整合Spring进行管理权限

### 1：Shiro简介

Apache Shiro是一个强大且易用的Java安全框架,执行身份验证、授权、密码学和会话管理。使用Shiro的易于理解的API,您可以快速、轻松地获得任何应用程序,从最小的移动应用程序到最大的网络和企业应用程序。

Shiro 主要分为来个部分就是认证和授权，在个人感觉来看就是查询数据库做相应的判断而已。Shiro的主要框架图如下

![img](https://img2018.cnblogs.com/blog/1174906/201809/1174906-20180930143107176-446270906.png)

是不是看到这张图，有点不想看了，有些混乱有些多，我就其中一些方法进行简要说明

**1：Subject**

Subject即主体，外部应用与subject进行交互，subject记录了当前操作用户，将用户的概念理解为当前操作的主体，可能是一个通过浏览器请求的用户，也可能是一个运行的程序。 Subject在shiro中是一个接口，接口中定义了很多认证授相关的方法，外部程序通过subject进行认证授，而subject是通过SecurityManager安全管理器进行认证授权。

**2：SecurityManager**

SecurityManager即安全管理器，对全部的subject进行安全管理，它是shiro的核心，负责对所有的subject进行安全管理。通过SecurityManager可以完成subject的认证、授权等，实质上SecurityManager是通过Authenticator进行认证，通过Authorizer进行授权，通过SessionManager进行会话管理等。

**3：Authorizer**

Authorizer即授权器，用户通过认证器认证通过，在访问功能时需要通过授权器判断用户是否有此功能的操作权限。

**4：Realm**

Realm即领域，相当于datasource数据源，securityManager进行安全认证需要通过Realm获取用户权限数据，比如：如果用户身份数据在数据库那么realm就需要从数据库获取用户身份信息。

**5：sessionManager**

sessionManager即会话管理，shiro框架定义了一套会话管理，它不依赖web容器的session，所以shiro可以使用在非web应用上，也可以将分布式应用的会话集中在一点管理，此特性可使它实现单点登录。

**6：SessionDAO**

SessionDAO即会话dao，是对session会话操作的一套接口，比如要将session存储到数据库，可以通过jdbc将会话存储到数据库。

**7：CacheManager**

CacheManager即缓存管理，将用户权限数据存储在缓存，这样可以提高性能。

**8：Cryptography**

Cryptography即密码管理，shiro提供了一套加密/解密的组件，方便开发。比如提供常用的散列、加/解密等功能。

### 2：Shiro认证过程

![img](https://images2018.cnblogs.com/blog/1174906/201808/1174906-20180812193118483-2054721945.png)

1）：引入shiro的jar包

```xml
 <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-core</artifactId>
      <version>1.4.0</version>
 </dependency>
```

2）：创建类文件

2.1构建SecurityManager环境

```java
DefaultSecurityManager defaultSecurityManager = new DefaultSecrityManager();
defaultSecurityManager.setRealm(simpleAccountRealm);
```

2.2主体提交认证请求

```java
SecurityUtils.setSecurityManager(defaultSecurityManager);
Subject subject = SecurityUtils.getSubject();
UsernamePasswordToKen token = new UsernamePassword(username : "小明"，password ：“123456”):
subject.login(token);
system.out.println("isAuthenticated:" + subject.isAuthenticated());
subject.logout();
system.out.println("isAuthenticated:" + subject.isAuthenticated());
```

### 2：Shiro授权过程

 ![img](https://images2018.cnblogs.com/blog/1174906/201808/1174906-20180813132510176-1037707258.png)

1）类文件

1.1构建SecurityManager环境

```java
 DefaultSecurityManager defaultSecurityManager = new DefaultSecrityManager();
defaultSecurityManager.setRealm(simpleAccountRealm);
```

1.2主体提交认证请求

```java
	SecurityUtils.setSecurityManager(defaultSecurityManager);
		Subject subject = SecurityUtils.getSubject();
		UsernamePasswordToKen token = new UsernamePassword(username : "小明"，password ：“123456”):	
		subject.login(token);
		system.out.println("isAuthenticated:" + subject.isAuthenticated());
		 subject.checkRole(s:"admin");	 subject.checkRoles(...string:"admin","user");
		 subject.logout();
		 system.out.println("isAuthenticated:" + subject.isAuthenticated());
```

### 3：关于Realm

Realm一般有三种，分别是，一般都使用自定义

1）IniRealm控制  .Ini 文件。

2）jdbcRealm控制 数据库文件

引入Mysql驱动包，引入数据源文件，设置jdbcurl以及用户名和密码

```java
JdbcRealm.jdbcRealm = new JdbcRealm();
jdbcRealm.setDataSource(dataSource);
jdbcRealm.setPermisssionsLookupEnabled(true);
```

主体提交验证

```java
subject.checkRole(s:"admin");
subject.checkRoles(...string:"admin","user");
subject.checkPermission(s:"user:select");
```

3）自定义realm

```java
package com.fuwh.realm;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

import com.fuwh.util.DbUtil;

 public class MyJdbcRealm extends AuthorizingRealm{

     @Override
     protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
         // TODO Auto-generated method stub
         return null;
     }

     @Override
     protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
         // TODO Auto-generated method stub
         Connection conn=DbUtil.getConnection();
         String sql="select * from members2 where username=?";
         try {
             PreparedStatement ps=conn.prepareStatement(sql);
             ps.setString(1, token.getPrincipal().toString());
             ResultSet rs=ps.executeQuery();
             while(rs.next()) {
                 AuthenticationInfo info=new SimpleAuthenticationInfo(rs.getString("username"),rs.getString("password"),"salt");
                 return info;
             }
         } catch (SQLException e) {
             // TODO Auto-generated catch block
             e.printStackTrace();
         }

         return null;
     }

 }
```

### 4：关于md5加密

4.1环境搭建及使用

```java
		CustomRealm customRealm = new CustomRealm();
		//1.构建SecurityManager 环境
		DefaultSecurityManager defaultSecurityManager = new DefaultSecrityManager();
		defalultSecurityManager.setRealm(customRealm);
		HashedCredentialsMatcher matcher = new HashedCredentialsMatcher();
		 matcher.setHashAlgorithmName("md5");
		 matcher.setHashIterations(1);
		 customRealm.setCredentialsMatcher(matcher);
		 //2.主体提交认证请求
		 SecurityUtils.setSecurityManager(defaultSecurityManager);
		 Subject subject = SecurityUtils.getSubject();
		 UsernamePasswordToken token = new UsernamePassowrdToken(username:"xiaoming",password:"123456");
		 subject.login(token);
		 System.out.println("isAuthenticated:" + subject.isAuthenticated())
```

4.2 MD5加密

```java
@Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthticationToken authenticationToken) throws AuthenticationRxception {
    //1.从主体传过来的认证信息中，获取用户名
    String userName = （String）authenticationToken.getPrincipal();
    //2.通过用户名到数据库获取凭证
     String password = getPasswordByUserName(userName);
     if(password == null){
       return null;
 	}
     SimpleAuthenticationInfo authticationInfo = new SimpleAuthenticationInfo (principal:“xiaoming”,password,realmName:"customRealm");
     authenticationInfo.setCredentialsSaltSalt(ByteSource.Util.bytes(string:"XQC"));
     return authenticationInfo;
}
```

##  四：Shiro在Web项目中的使用

　　实战中shiro的应用很多，几乎都要用到，这里举一个Blog的登陆的例子，更好的理解和使用。本博客是采用spring+springMVC+Mybatis实现的。

### 　　1：执行流程

![img](https://images2018.cnblogs.com/blog/1174906/201808/1174906-20180820201742575-1256571717.png)

### 2：登录页面 login.jsp

```html
<!--只截取form表单部分-->
<form action="${pageContext.request.contextPath}/blogger/login.do" method="post" onsubmit="return checkForm()">
    <DIV style="background: rgb(255, 255, 255); margin: -100px auto auto; border: 1px solid rgb(231, 231, 231); border-image: none; width: 400px; height: 200px; text-align: center;">
        <DIV style="width: 165px; height: 96px; position: absolute;">
            <DIV class="tou">
            </DIV>
            <DIV class="initial_left_hand" id="left_hand">
            </DIV>
            <DIV class="initial_right_hand" id="right_hand">
             </DIV>
         </DIV>
         <P style="padding: 30px 0px 10px; position: relative;">
             <SPAN class="u_logo"></SPAN>
             <INPUT id="userName" name="userName" class="ipt" type="text" placeholder="请输入用户名" value="${blogger.userName }">
         </P>
         <P style="position: relative;">
             <SPAN class="p_logo"></SPAN>
             <INPUT id="password" name="password" class="ipt"  type="password" placeholder="请输入密码" value="${blogger.password }">
           </P>
         <DIV style="height: 50px; line-height: 50px; margin-top: 30px; border-top-color: rgb(231, 231, 231); border-top-width: 1px; border-top-style: solid;">
             <P style="margin: 0px 35px 20px 45px;">
             <span><font color="red" id="error">${errorInfo }</font></span>
             <SPAN style="float: right;">
                   <input type="submit" style="background: rgb(0, 142, 173); padding: 7px 10px; border-radius: 4px; border: 1px solid rgb(26, 117, 152); border-image: none; color: rgb(255, 255, 255); font-weight: bold;" value="登录"/>
              </SPAN>
              </P>
         </DIV>
     </DIV>
 </form>
```

### 3：Controller层的控制

```java
package com.xqc.controller;

/**
 * 博主Controller层
 *
 */
@Controller
@RequestMapping("/blogger")
public class BloggerController {

     @Resource
     private BloggerService bloggerService;

     /**
      * 用户登录
      * @param blogger
      * @param request
      * @return
      */
     @RequestMapping("/login")
     public String login(Blogger blogger,HttpServletRequest request){
         Subject subject=SecurityUtils.getSubject();
         UsernamePasswordToken token=new UsernamePasswordToken(blogger.getUserName(), CryptographyUtil.md5(blogger.getPassword(), "xqc"));
         try{
             subject.login(token); // 登录验证
             return "redirect:/admin/main.jsp";
         }catch(Exception e){
             e.printStackTrace();
             request.setAttribute("blogger", blogger);
             request.setAttribute("errorInfo", "用户名或密码错误！");
             return "login";
         }
     }
 }
```

### 4：在spring的配置文件中添加shiro的过滤器

```xml
    <!-- Shiro过滤器 -->
    <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
        <!-- Shiro的核心安全接口,这个属性是必须的 -->
        <property name="securityManager" ref="securityManager"/>
        <!-- 身份认证失败，则跳转到登录页面的配置 -->
        <property name="loginUrl" value="/login.jsp"/>
        <!-- Shiro连接约束配置,即过滤链的定义 -->
        <property name="filterChainDefinitions">
            <value>
                 /login=anon
                 /admin/**=authc
             </value>  
         </property>
     </bean>    
```

### 5：拦截后交给自定义Realm进行验证。

```java
package com.xqc.realm;

import javax.annotation.Resource;


import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

import com.xqc.entity.Blogger;
import com.xqc.service.BloggerService;

/**
 * 自定义Realm
 *
 */
public class MyRealm extends AuthorizingRealm{

    @Resource
    private BloggerService bloggerService;

    /**
     * 为当限前登录的用户授予角色和权
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        return null;
    }

    /**
     * 验证当前登录的用户
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        String userName=(String)token.getPrincipal();
        Blogger blogger=bloggerService.getByUserName(userName);
        if(blogger!=null){
            SecurityUtils.getSubject().getSession().setAttribute("currentUser", blogger); // 当前用户信息存到session中
            AuthenticationInfo authcInfo=new SimpleAuthenticationInfo(blogger.getUserName(),blogger.getPassword(),"xx");
            return authcInfo;
        }else{
            return null;
        }
    }

}
```

### 6：并用工具类进行加盐

```java
package com.xqc.util;
import org.apache.shiro.crypto.hash.Md5Hash;
/**
 * 加密工具
 *
 */
public class CryptographyUtil {
    /**
     * Md5加密
     * @param str
     * @param salt
     * @return
     */
    public static String md5(String str,String salt){
        return new Md5Hash(str,salt).toString();
    }
}
```

 分角色赋予不同的权利啦！分角色回显不同的信息啦！

 