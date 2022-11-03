# 一、Maven介绍

Maven是基于项目对象模型(POM)，可以通过一小段描述信息来管理项目的构建，报告和文档的软件项目管理工具。

Maven是跨平台的项目管理工具。主要服务于基于Java平台的**项目构建**，**依赖管理**和**项目信息管理**。

Maven主要有两个功能：

1.  项目构建

2.  依赖管理

构建过程：清理-编译-测试-报告-打包-部署

## 项目构建的方式

1、Eclipse

手工操作较多，项目的构建过程都是独立的，很难一步完成。比如：编译、测试、部署等。

开发时每个人的IDE配置都不同，很容易出现本地代码换个地方编译就出错

2、Ant

Ant只是一个项目构建工具，它没有集成依赖管理。

Ant在进行项目构建时，它没有对项目目录结构进行约定，需要手动指定源文件、类文件等目录地址。同时它执行task时，需要显示指定依赖的task，这样会造成大量的代码重复。

3、**Maven**

Maven不仅是一个项目构建工具，更是一个项目管理工具。它在项目构建工程中，比ant更全面，更灵活。

Maven在进行项目构建时，它对项目目录结构拥有约定，知道你的源代码在哪里，类文件应该放到哪里去。

它拥有生命周期的概念，maven的生命周期是有顺序的，在执行后面的生命周期的任务时，不需要显示的配置前面任务的生命周期。例如执行
mvn install 就可以自动执行编译，测试，打包等构建过程

## Maven模型

![E:\\工作\\java\\课件\\17-Maven\\讲义\\maven模型.jpg](media/148482bf9d9fbdd7a7f58cdbd9a53b3d.jpeg)

# 二、安装配置

## 安装

下载maven：官方网站：<http://maven.apache.org>

第一步：安装jdk

第二步：把maven解压缩，解压目录最好不要有中文。

```txt
D:\Maven\install\apache-maven-3.5.4
```

第三步：配置环境变量MAVEN_HOME

![image-20201214164746895](media/image-20201214164746895.png)

第四步：配置环境变量PATH，将%MAVEN_HOME%\\bin加入Path中，在Windows中一定要注意要用分号；与其他值隔开。

第五步：验证是否安装成功，打开cmd窗口，输入mvn –v

## 配置

Maven有两个settings.xml配置文件，一个是全局配置文件，一个是用户配置文件。

1：全局配置（默认）

**%MAVEN_HOME%/conf/settings.xml** 是maven全局的配置文件。

该配置文件中配置了本地仓库的路径，默认就是：\~/.m2/repository。其中\~表示当前用户路径C:\\Users\\[UserName]。

localRepository：用户仓库，用于检索依赖包路径

2：用户配置

**\~/.m2/settings.xml**是用户的配置文件（默认没有该文件，需要将全局配置文件拷贝过来在进行修改）

注意：一般本地仓库的地址不使用默认配置，通常情况下需要在用户配置中，配置新的仓库地址。

配置步骤如下：

第一步：创建一个本地仓库目录，比如E:\repository

第二步：复制maven的全局配置文件到\~/.m2目录下，即创建用户配置文件

第三步：修改maven的用户配置文件

```xml
<localRepository>E:\repository</localRepository>
```

**注意：**

**用户级别的仓库在全局配置中一旦设置，全局配置将不再生效，转用用户所设置的仓库，否则使用全局配置文件中的默认路径仓库。**




# 三、Maven的命令

需要在pom.xml所在目录中执行以下命令。

```txt
Mvn compile
执行 mvn compile命令，完成编译操作
执行完毕后，会生成target目录，该目录中存放了编译后的字节码文件。


 Mvn clean
执行 mvn clean命令
执行完毕后，会将target目录删除。

Mvn test
执行 mvn test命令，完成单元测试操作
执行完毕后，会在target目录中生成三个文件夹：surefire、surefire-reports（测试报告）、test-classes（测试的字节码文件）

Mvn package
执行 mvn package命令，完成打包操作
执行完毕后，会在target目录中生成一个文件，该文件可能是jar、war


Mvn install 
执行 mvn install命令，完成将打好的jar包安装到本地仓库的操作
执行完毕后，会在本地仓库中出现安装后的jar包，方便其他工程引用


mvn clean compile命令
cmd 中录入 mvn clean compile命令
组合指令，先执行clean，再执行compile，通常应用于上线前执行，清除测试类


 mvn clean test命令
cmd 中录入 mvn clean test命令
组合指令，先执行clean，再执行test，通常应用于测试环节


 mvn clean package命令
cmd 中录入 mvn clean package命令
组合指令，先执行clean，再执行package，将项目打包，通常应用于发布前

执行过程：
清理————清空环境
编译————编译源码
测试————测试源码
打包————将编译的非测试类打包


mvn clean install命令
cmd 中录入 mvn clean install 查看仓库，当前项目被发布到仓库中
组合指令，先执行clean，再执行install，将项目打包，通常应用于发布前

执行过程：

清理————清空环境
编译————编译源码
测试————测试源码
打包————将编译的非测试类打包
部署————将打好的包发布到资源仓库中

```



# 四、Maven核心概念

## 坐标

Maven世界拥有大量构建，我们需要找一个用来唯一标识一个构建的统一规范；拥有了统一规范，就可以把查找工作交给机器

Maven坐标主要组成：

```txt
groupId：定义当前Maven组织名称
artifactId：定义实际项目名称
version：定义当前项目的当前版本
```

## 依赖管理

就是对项目中jar 包的管理。可以在pom文件中定义jar包的GAV坐标，管理依赖。

依赖声明主要包含如下元素：

```xml
<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.10</version>
			<scope>test</scope>
		</dependency>		
	</dependencies>
```

1：依赖范围

![E:\\工作\\java\\课件\\17-Maven\\讲义\\依赖范围.JPG](media/763bf5c00ed21a0df070e4f93419842c.jpeg)

其中依赖范围**scope** 用来控制依赖和编译，测试，运行的classpath的关系，主要的是三种依赖关系如下：

compile： 默认编译依赖范围。对于编译，测试，运行三种classpath都有效

test：测试依赖范围。只对于测试classpath有效

provided：已提供依赖范围。对于编译，测试的classpath都有效，但对于运行无效。因为由容器已经提供，例如servlet-api

runtime:运行时提供。例如:jdbc驱动



2：依赖传递

如果C中使用B，B中使用A，则称B是C的**直接依赖，**而称A是C的**间接依赖**。



3：依赖范围对传递依赖的影响

![E:\\工作\\java\\课件\\17-Maven\\讲义\\传递性依赖影响范围.jpg](media/9532ba88e1328a203b2acfc6d7d55064.jpeg)

左边第一列表示第一直接依赖范围

上面第一行表示第二直接依赖范围

中间的交叉单元格表示传递性依赖范围。

总结：

-   当第二依赖的范围是compile的时候，传递性依赖的范围与第一直接依赖的范围一致。

-   当第二直接依赖的范围是test的时候，依赖不会得以传递。

-   当第二依赖的范围是provided的时候，只传递第一直接依赖范围也为provided的依赖，且传递性依赖的范围同样为
    provided；

-   当第二直接依赖的范围是runtime的时候，传递性依赖的范围与第一直接依赖的范围一致，但compile例外，此时传递的依赖范围为runtime；



4：依赖冲突

如果直接与间接依赖中包含有同一个坐标不同版本的资源依赖，以直接依赖的版本为准（就近原则）

如果直接依赖中包含有同一个坐标不同版本的资源依赖，以配置顺序下方的版本为准（就近原则）



5：可选依赖

\<optional\> true/false 是否可选，也可以理解为是否向下传递。

在依赖中添加optional选项决定此依赖是否向下传递，如果是true则不传递，如果是false就传递，默认为false。



6：排除依赖

```xml
<exclusions>
	<exclusion>
		<groupId>cn.xqc.maven</groupId>
		<artifactId>maven-first</artifactId>
	</exclusion>
</exclusions>
```


排除依赖包中所包含的依赖关系，**不需要添加版本号**。

如果在本次依赖中有一些多余的jar包也被传递依赖过来，如果想把这些jar包排除的话可以配置exclusions进行排除。

## 生命周期

Maven生命周期就是为了**对所有的构建过程进行抽象和统一**。包括项目清理、初始化、编译、打包、测试、部署等几乎所有构建步骤。

**生命周期可以理解为构建工程的步骤。**

在Maven中有三套相互独立的生命周期，请注意这里说的是“三套”，而且“相互独立”，这三套生命周期分别是：

-   Clean Lifecycle： 在进行真正的构建之前进行一些清理工作。

-   Default Lifecycle： 构建的核心部分，编译，测试，打包，部署等等。

-   Site Lifecycle： 生成项目报告，站点，发布站点。

再次强调一下它们是相互独立的，你可以仅仅调用clean来清理工作目录，仅仅调用site来生成站点。当然你也可以直接运行
mvn clean install site 运行所有这三套生命周期。

### 三大生命周期

1：clean：清理项目

每套生命周期都由一组阶段(Phase)组成，我们平时在命令行输入的命令总会对应于一个特定的阶段。比如，运行mvn  clean，这个的clean是Clean生命周期的一个阶段。有Clean生命周期，也有clean阶段。Clean生命周期一共包含了三个阶段：

pre-clean 执行一些需要在clean之前完成的工作

clean 移除所有上一次构建生成的文件

post-clean 执行一些需要在clean之后立刻完成的工作

mvn clean中的clean就是上面的clean，在一个生命周期中，运行某个阶段的时候，它之前的所有阶段都会被运行，也就是说，mvn clean 等同于 mvn pre-clean clean ，如果我们运行 mvn post-clean ，那么pre-clean，clean都会被运行。这是Maven很重要的一个规则，可以大大简化命令行的输入。

2：default：构建项目

Default生命周期是Maven生命周期中最重要的一个，绝大部分工作都发生在这个生命周期中。这里，只解释一些比较重要和常用的阶段：

validate

generate-sources

process-sources

generate-resources

process-resources 复制并处理资源文件，至目标目录，准备打包。

**compile** 编译项目的源代码。

process-classes

generate-test-sources

process-test-sources

generate-test-resources

process-test-resources 复制并处理资源文件，至目标测试目录。

test-compile 编译测试源代码。

process-test-classes

**test** 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。

prepare-package

**package** 接受编译好的代码，打包成可发布的格式，如 JAR 。

pre-integration-test

integration-test

post-integration-test

verify

**install** 将包安装至本地仓库，以让其它项目依赖。

deploy 将最终的包复制到远程的仓库，以让其它开发人员与项目共享。

**运行任何一个阶段的时候，它前面的所有阶段都会被运行**，这也就是为什么我们运行mvninstall的时候，代码会被编译，测试，打包。此外，Maven的插件机制是完全依赖Maven的生命周期的，因此理解生命周期至关重要。

3：site：生成项目站点

Site生命周期

pre-site 执行一些需要在生成站点文档之前完成的工作

site 生成项目的站点文档

post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备

site-deploy 将生成的站点文档部署到特定的服务器上

这里经常用到的是site阶段和site-deploy阶段，用以生成和发布Maven站点，这可是Maven相当强大的功能，Manager比较喜欢，文档及统计数据自动生成，很好看。

## Maven插件

Maven的核心仅仅定义了抽象的生命周期，具体的任务都是交由插件完成的。每个插件都能实现一个功能，每个功能就是一个插件目标。Maven的生命周期与插件目标相互绑定，以完成某个具体的构建任务。

例如compile就是插件maven-compiler-plugin的一个插件目标

1：Maven编译插件

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-compiler-plugin</artifactId>
	<configuration>
		<source>1.7</source>
		<target>1.7</target>
		<encoding>UTF-8</encoding>
	</configuration>
</plugin>

```

修改配置文件后，在工程上点击右键选择maven→update project configration

2：Tomcat插件

## 聚合

聚合一般是一个工程拆分成多个模块开发，每个模块是一个独立的工程，但是要是运行时必须把所有模块聚合到一起才是一个完整的工程，此时可以使用maven的聚合工程。

例如电商项目中，包括商品模块、订单模块、用户模块等。就可以对不同的模块单独创建工程，最终在打包时，将不同的模块聚合到一起。

例如同一个项目中的表现层、业务层、持久层，也可以分层创建不同的工程，最后打包运行时，再聚合到一起。

# 五、Maven仓库管理

## 分类

1、本地仓库

\~/.m2/repository

每个用户只有一个本地仓库

2、远程仓库

-   中央仓库：Maven默认的远程仓库，不包含版权资源

<http://repo1.maven.org/maven2>

-   私服：是一种特殊的远程仓库，它是架设在局域网内的仓库

## 私服

### 安装Nexus

# 问题

1：手动添加jar到Maven仓库

**常用Maven仓库网址：**
http://mvnrepository.com/
http://search.maven.org/
http://repository.sonatype.org/content/groups/public/
http://people.apache.org/repo/m2-snapshot-repository/
http://people.apache.org/repo/m2-incubating-repository/

```xml
<!-- https://mvnrepository.com/artifact/ojdbc/ojdbc --><!-- (参数一)：下载到本地的ojdbc-10.2.0.4.0.jar包的真实存放路径 -->
<dependency>
    <groupId>ojdbc</groupId>-----------------(参数二)
    <artifactId>ojdbc</artifactId>-----------(参数三)
    <version>10.2.0.4.0</version>------------(参数四)
</dependency>
```

用maven命令将jar包移动到maven的本地repository中。

语法：

```cmd
mvn install:install-file -Dfile=jar包的位置(参数一) -DgroupId=groupId(参数二) -DartifactId=artifactId(参数三) -Dversion=version(参数四) -Dpackaging=jar
```

 我把“ojdbc-10.2.0.4.0.jar”放到了“D:\Program Files\mvn\”下，

**注意：**“Program Files”中间**有空格**，所以要加双引号，另外三个参数，从上面复制过来即可，下面是我安装ojdbc-10.2.0.4.0.jar包使用的命令：

```cmd
mvn install:install-file -Dfile=``"D:\Program Files\mvn\ojdbc-10.2.0.4.0.jar"` `-DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.4.0 -Dpackaging=jar
```

**需要注意以下几点：
1.注意"-"不能缺少 install后面的"-"是没有空格的
2.注意"-Dfile"中jar包的路径和jar包的名字.
3.注意看cmd命令提示,查看本地repository中是否成功的复制了jar包.**



