

纯Java编写的，19年3月在中国开启了CDN，源代码在Github上，可以贡献；发布比较快，最大的问题就是如何保持稳定，可以使用Gradle wrapper；

Gradle是基于Groovy语言的，书写上支持Groovy脚本；

# 一：安装配置

下载：https://gradle.org/releases/ 

配置环境变量

变量名：GRADLE_HOME

变量值：D:\Gradle\gradle-6.3（解压路径名）

在系统变量 path中加入：%GRADLE_HOME%\bin;



如果你想使用本地原本的Maven仓库，只需要再添加

变量名：GRADLE_USER_HOME

变量值：F:\MavenRepository

这个变量也是Gradle的仓库存放地址信息，默认是：C:/Users/(用户名)/.gradle/caches/modules-2/files-2.1

2：目录结构：

bin：命令

gradle/userhome

gradle/cache：缓存

## 核心模型

在Gradle的配置文件中有项目project和任务task的概念；

task代表着Gradle构建过程中可执行的最小单元。例如当构建一个组件时，可能需要先编译、打包、然后再生成文档或者发布等，这其中的每个步骤都可以定义成一个task

daem模式：Maven启动JVM 然后Load Jar的过程比较慢，用完就将这个JVM销毁了；使用Daemon 模式，会有Gradle Client 会去Daemon 中取，Daemon JVM一直存在，如果Gradle Client 声明 --no--daemon，就会和Maven一样了；

## 依赖管理

gradle通过group，name，version唯一确定某jar包，这点和Maven相似；

```groovy
dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.11'
}
```

dependencies代表所依赖的类，这里只有一个junit，并且只是在testCompile阶段才起作用，此处写明了group，name，version，

版本冲突问题：

在maven中遇到版本冲突时，需要打印依赖树，然后自己手动去找哪个依赖中带有冲突的版本包，然后再通过移除解决冲突

在gradle构建中，默认依赖最高版本的jar包，这样就方便多了

如果我们想修改默认策略，首先需要设置当版本冲突时，构建失败，如下：

```groovy
configurations.all {
    resolutionStrategy {
        failOnVersionConflict()
    }
}
```

然后有两种方式修改依赖版本：

1.排除传递性依赖：exclude group:xxx module:xxx (module就是name)

2.强制版本：在上面的failOnVersionConflict方法下写上force版本，这里force也可以指定非当前依赖的版本

## 配置文件

`settings.gradle ` 

用来管理多项目，里面包含了项目的name

` build.gradle ` 

dependencies：用来声明项目依赖于哪些jar

## 生命周期：

initializaition：初始化阶段，读取构建，决定是但项目构建还是多项目构建，决定哪些项目会参与到这次构建中，

Configuration：配置阶段，不运行构建，只是对构建进行一些特殊的配置；本质上就是将` build,gradle ` 从头执行到尾；会当成Groovy执行

Execution：执行阶段，执行哪些task；complie Java



执行命令：

```shell
./gradlew help
./gradle helloworld
```

## 多项目构建

- 多项目集成：setting.gradle中include子项目名称
- 子项目依赖：在depencies中写上compile project(":项目名")
- 提取公共信息到根项目：在根配置中编写allprojects，如编译版本、java插件
- 统一所有项目版本：新建gradle.properties，设置版本

## 发布

```groovy
apply plugin: 'maven-publish'

publishing {
    publications {
        myPublish(MavenPublication) {
            from components.java
        }
    }
    repositories {
        maven {
            name "名称"
            url "要发布的地址"
        }
    }
}
```



# 三：Groovy基础

运行在JVM上的脚本语言；完全兼容Java语法；

```groovy
println "hello groovy"

//定义变量
def i = 18

//定义一个集合类型
def list = ['a','b']

//往list中添加元素
list<< 'c'
//取出
println list.get(2)
```

闭包：

其实就是一段代码块



# 四：插件编写

公用的东西写成一个插件，其他项目直接调用插件

```groovy
apply([plugin:MyAwesomePlugin])
或 
apply plugin :MyAwesomePlugin
```

