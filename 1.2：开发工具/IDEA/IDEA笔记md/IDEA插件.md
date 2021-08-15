# 插件推荐

## 热部署插件

1：安装并重启

File——Setting——Plugins——MarketPlace搜索 ` JRebel and XRebel ` 

2：提示 Enable jrebel 点击使用，根据设置导航进行设置

<img src="https://nulleringnotepic.oss-cn-hangzhou.aliyuncs.com/notepic/image-20201027171310294-162781803187910.png" alt="image-20201027171310294" style="zoom:50%;" />

3：激活

（1）：点此链接生成 guid： https://www.guidgen.com/ 复制生成的 guid

（2）：选择第一个选项 Team URL

在第一个空白地址中填写：

```txt
https://jrebel.qekang.com/+生成的guid
```

第二个选项则填写你自己的邮箱即可

然后选择同意接受协议等等

4：然后继续点击右侧的  ` JRebel setup guide ` 

5：设置自动编译

File——Setting——Build，Execution，Deployment ——Complier——右侧找到 ` Bulid project Automatically ` ，将前面的√勾选上；

6：设置运行时编译

7：修改后手动更新 ctrl+shift +F9

以后就不要再使用 debug 启动了，使用  ` Jrebel debug  ` 启动即可。

![image-20201029161430028](https://nulleringnotepic.oss-cn-hangzhou.aliyuncs.com/notepic/image-20201029161430028.png)

## MybatisCodeHelper

1：下载与安装

蓝奏云("https://borber.lanzous.com/b0cq9t1jc") 密码:6llf

然后直接拖拽进 IDEA，重启 IDEA

按照以下方法激活即可

2：激活

IDEA顶部菜单：Tools——MybatisCodeHelper——Activation——OffineActivation，在offline key框输入任意字符串

3：使用功能

（1）根据实体类生成建表语句

（2）根据表生成 bean，mapper.xml：连接数据库，打开 DataBase 窗口，选择要生成的代码对应的表，右键

<img src="https://nulleringnotepic.oss-cn-hangzhou.aliyuncs.com/notepic/1645656-20200317085555545-1809723491-16278180318794.png" alt="img" style="zoom:50%;" />

（3）点击小鸟可实现 Dao 与 mapper.xml 文件的跳转

## Grep Console

这是一个可以实现控制台彩色输出的插件，比如可以设置ERROR 和 SQL等以彩色方式输出

1：安装插件

File——Setting——Plugins——MarketPlace搜索 ` Grep Console ` 

2：设置：个人设置如下，可供参考

![image-20201026212441919](https://nulleringnotepic.oss-cn-hangzhou.aliyuncs.com/notepic/image-20201026212441919.png)

我是配合打印 SQL 语句，这样直观；如果你还不知道如何打印 SQL 语句，可以参考我的文章使用拦截器，或者使用另一个插件MybatisLog

## Mybatis Log

控制台打印出的一般是没有拼装好的SQL，MybatisLog可以帮你打印出拼装好的SQL语句，帮助你检查问题；

1：下载安装

下载链接：https://www.aliyundrive.com/s/FvUhbXKGLJe

安装：File——Setting——Plugins——找到Install右侧设置按钮——下拉选择：Install Plugin from Disk——然后选择刚刚下载的插件

2：配置打印

在mybatis配置文件中添加如下内容

```xml
  <setting name="logImpl" value="STDOUT_LOGGING" />
```

## SonarLint

这个插件是配合 SonarQube 代码质量管理平台进行使用的，不过如果你的团队没有进行代码质量管理，你也可以安装此插件自己使用，进行代码质量管理的自检。安装上即可使用，如果想在团队进行 SonarQube 质量管理，可以再进行 SonarQube 配置。

同样的还有Alibaba Java Coding Guidelines（阿里巴巴的编码规约检查插件）

检查你的编码习惯，让你更规范

## FindBug 或 spotBugs 

FindBug 新版不能用了，用 spotBugs 代替，它可以帮助寻找潜在的 Bug

## SequenceDiagram

调用链路自动生成时序图

双击顶部的类名可以跳转到对应类的源码中，双击调用的函数名可以直接调入某个函数的源码。

## Statistic

代码统计

## Maven Helper

帮助解决 Maven 冲突

## CamelCase

驼峰式命名和下划线命名交替变化

## CodeGlance

代码迷你缩放图插件

## Material Theme UI

那就顺便推荐一下这个吧，超多的主题插件，各种颜色，各种模式，感兴趣的可以试一下，图我就不截了

## Translation

 翻译插件 ：将英文翻译成中文，阅读源码不再费劲；将中文翻译成英文，给接口起名字就不用费劲啦

# 开发插件

如果还不能满足你的需求，那就自己开发一款插件吧！

新建项目：

![image-20210706211109671](https://nulleringnotepic.oss-cn-hangzhou.aliyuncs.com/notepic/image-20210706211109671.png)

推荐使用社区版的 IDEA，这样可以阅读 IDEA 的源码：

版本配置

1：gradle 配置

```gradle
//默认会整成最新的JDk，这样导致有的项目不能用
sourceCompatibility = 1.8
//默认会整成最新的IDEA才能用，定义一个老一点的版本
intellij{
	version='2017.3'
	updateSinceUntilBuild=false
}
```

2：插件配置

resource-MATE-INF 目录下的 plugin.xml

具体看下边

3：打包成一个 zip 压缩包，

自己的话自己从硬盘上安装就可以了

4：发布上传

插件官网：https://plugins.jetbrains.com/

注册账号进行登录

直接点击自己头像下的 upload plugin，然后选择刚打包好的 zip 文件

上传后，人家官方审核，审核通过即可从插件库下载到；

## 1：给插件设置图标

在 MATE-INF 文件夹下新建 pluginicorn.svg ，名称固定，

## 2：配置

```xml
<!--兼容性配置-->
<idea-version since-build="171.0"></idea-version>
<description>
</description>
```
