 本文的目的是搞清楚 Java 中各种日志 Log 之间是怎么的关系，如何作用、依赖，好让我们平时在工作中如果遇到“日志打不出”或者“日志 jar 包冲突”等之类的问题知道该如何入手解决，以及在各种场景下如何调整项目中的各个框架的日志输出，使得输出统一。

在日常工作中我们可能看到项目中依赖的跟日志相关的 jar 包有很多，`commons-logging.jar`、`log4j.jar`、`sl4j-api.jar`、`logback.jar`等等，眼花缭乱。我们要正确的配置，使得 jar 包相互作用生效之前，就先要理清它们之间的关系。

发展史：

那就要从 Java Log 的发展历程开始说起。

1. `log4j`（作者 Ceki Gülcü）出来时就等到了广泛的应用（注意这里是直接使用），是 Java 日志事实上的标准，并成为了 Apache 的项目
2. Apache 要求把 log4j 并入到 JDK，SUN 拒绝，并在 jdk1.4 版本后增加了`JUL`（`java.util.logging`）
3. 毕竟是 JDK 自带的，JUL 也有很多人用。同时还有其他日志组件，如 SimpleLog 等。这时如果有人想换成其他日志组件，如 log4j 换成 JUL，因为 api 完全不同，就需要改动代码。
4. Apache 见此，开发了`JCL`（Jakarta Commons Logging），即`commons-logging-xx.jar`。它只提供一套通用的日志接口 api，并不提供日志的实现。很好的设计原则嘛，依赖抽象而非实现。这样应用程序可以在运行时选择自己想要的日志实现组件。
5. 这样看上去也挺美好的，但是 log4j 的作者觉得 JCL 不好用，自己开发出`slf4j`，它跟 JCL 类似，本身不替供日志具体实现，只对外提供接口或门面。目的就是为了替代 JCL。同时，还开发出`logback`，一个比 log4j 拥有更高性能的组件，目的是为了替代 log4j。
6. Apache 参考了 logback,并做了一系列优化，推出了`log4j2`

## 概述

体系

![image-20210104092014718](media/image-20210104092014718.png)

**日志门面**

日志门面定义了一组日志的接口规范，它并不提供底层具体的实现逻辑。`Apache Commons Logging` 和 `Slf4j` 就属于这一类。

**日志实现**

日志实现则是日志具体的实现，包括日志级别控制、日志打印格式、日志输出形式（输出到数据库、输出到文件、输出到控制台等）。`Log4j`、`Log4j2`、`Logback` 以及 `Java Util Logging` 则属于这一类。

### **日志级别**

使用日志级别的好处在于，调整级别，就可以屏蔽掉很多调试相关的日志输出。不同的日志实现定义的日志级别不太一样，不过也都大同小异。

**Java Util Logging**

`Java Util Logging` 定义了 7 个日志级别，从严重到普通依次是：

- SEVERE
- WARNING
- INFO
- CONFIG
- FINE
- FINER
- FINEST

因为默认级别是 INFO，因此 INFO 级别以下的日志，不会被打印出来。

**Log4j**

`Log4j` 定义了 8 个日志级别（除去 OFF 和 ALL，可以说分为 6 个级别），从严重到普通依次是：

- OFF：最高等级的，用于关闭所有日志记录。
- FATAL：重大错误，这种级别可以直接停止程序了。
- ERROR：打印错误和异常信息，如果不想输出太多的日志，可以使用这个级别。
- WARN：警告提示。
- INFO：用于生产环境中输出程序运行的一些重要信息，不能滥用。
- DEBUG：用于开发过程中打印一些运行信息。
- TRACE
- ALL 最低等级的，用于打开所有日志记录。

**Logback**

`Logback` 日志级别比较简单，从严重到普通依次是：

- ERROR
- WARN
- INFO
- DEBUG
- TRACE

**对比**

`Java Util Logging` 系统在 `JVM` 启动时读取配置文件并完成初始化，一旦应用程序开始运行，就无法修改配置。另外，这种日志实现配置也不太方便，只能在 `JVM` 启动时传递参数，像下面这样：

```
-Djava.util.logging.config.file=<config-file-name>。
```

由于这些局限性，导致 `Java Util Logging` 并未广泛使用。

`Log4j` 虽然配置繁琐，但是一旦配置完成，使用起来就非常方便，只需要将相关的配置文件放到 `classpath` 下即可。在很多情况下，`Log4j` 的配置文件我们可以在不同的项目中反复使用。

`Log4j` 可以和 `Apache Commons Logging` 搭配使用，`Apache Commons Logging` 会自动搜索并使用 `Log4j`，如果没有找到 `Log4j`，再使用 `Java Util Logging`。

比 `Log4j` + `Apache Commons Logging` 组合更得人心的是 `Slf4j` + `Logback` 组合。

`Logback` 是 `Slf4j` 的原生实现框架，它也出自 `Log4j` 作者（Ceki Gülcü）之手，但是相比 `Log4j`，它拥有更多的优点、特性以及更强的性能。

- 如果不想添加任何依赖，使用 `Java Util Logging` 或框架容器已经提供的日志接口。
- 如果比较在意性能，推荐：`Slf4j` + `Logback`。
- 如果项目中已经使用了 `Log4j` 且没有发现性能问题，推荐组合为：`Slf4j` + `Log4j2`。

JCL

`commons-logging`已经停止更新，最后的状态如下所示：

![image-20201229144640097](media/image-20201229144640097.png)

## SLF4j

意思为如果你想用 slf4j 作为日志门面的话，你如何去配合使用其他日志实现组件，这里说明一下（注意 jar 包名缺少了版本号，在找版本时也要注意版本之间是否兼容）

- slf4j + logback`slf4j-api.jar` + `logback-classic.jar` + `logback-core.jar`
- slf4j + log4j`slf4j-api.jar` + `slf4j-log4j12.jar` + `log4j.jar`
- slf4j + jul`slf4j-api.jar` + `slf4j-jdk14.jar`
- 也可以只用 slf4j 无日志实现`slf4j-api.jar` + `slf4j-nop.jar`

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

#### 输出到文件

如果想禁止控制台的日志输出，转而将日志内容输出到一个文件，我们可以自定义一个 `logback-spring.xml` 文件，并引入前面所说的 `file-appender.xml` 文件。

像下面这样（`resources/logback-spring.xml`）：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml" />
    <property name="LOG_FILE" value="${LOG_FILE:-${LOG_PATH:-${LOG_TEMP:-${java.io.tmpdir:-/tmp}}/}spring.log}"/>
    <include resource="org/springframework/boot/logging/logback/file-appender.xml" />
    <root level="INFO">
        <appender-ref ref="FILE" />
    </root>
</configuration>
```

logback 配置

默认的 `Logback` 配置文件名有两种：

- `logback.xml`：这种配置文件会直接被日志框架加载。
- `logback-spring.xml`：这种配置文件不会被日志框架直接加载，而是由 Spring Boot 去解析日志配置，可以使用 Spring Boot 的高级 Profile 功能。

Spring Boot 中为 `Logback` 提供了四个默认的配置文件，位置在 `org/springframework/boot/logging/logback/`，分别是：

- defaults.xml：提供了公共的日志配置，日志输出规则等。
- console-appender.xml：使用 CONSOLE_LOG_PATTERN 添加一个 ConsoleAppender。
- file-appender.xml：添加一个 RollingFileAppender。
- base.xml：为了兼容旧版 Spring Boot 而提供的。

如果需要自定义 `logback.xml` 文件，可以在自定义时使用这些默认的配置文件，也可以不使用。一个典型的 `logback.xml` 文件如下（resources/logback.xml）：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <include resource="org/springframework/boot/logging/logback/console-appender.xml" />
    <root level="INFO">
        <appender-ref ref="CONSOLE" />
    </root>
    <logger name="org.springframework.web" level="DEBUG"/>
</configuration>
```

可以通过 include 引入 Spring Boot 已经提供的配置文件，也可以自定义。

Spring

Spring 是用 JCL 作为日志门面的

## Log4J

Log4j有三个主要的组件：Loggers(记录器)、Appenders(输出源)和Layouts(布局)。

这里可简单理解为日志类别，日志要输出的地方和日志以何种形式输出。综合使用这三个组件可以轻松地记录信息的类型和级别，并可以在运行时控制日志输出的样式和位置。

**1、Loggers**

Loggers组件在此系统中被分为五个级别：DEBUG、INFO、WARN、ERROR和FATAL。这五个级别是有顺序的，DEBUG < INFO < WARN < ERROR < FATAL，分别用来指定这条日志信息的重要程度，明白这一点很重要，Log4j有一个规则：只输出级别不低于设定级别的日志信息，假设Loggers级别设定为INFO，则INFO、WARN、ERROR和FATAL级别的日志信息都会输出，而级别比INFO低的DEBUG则不会输出。

**2、Appenders**

禁用和使用日志请求只是Log4j的基本功能，Log4j日志系统还提供许多强大的功能，比如允许把日志输出到不同的地方，如控制台（Console）、文件（Files）等，可以根据天数或者文件大小产生新的文件，可以以流的形式发送到其它地方等等。

常使用的类如下：

```
org.apache.log4j.ConsoleAppender（控制台）
org.apache.log4j.FileAppender（文件）
org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）
```

配置模式：

```
log4j.appender.appenderName = className
log4j.appender.appenderName.Option1 = value1
…
log4j.appender.appenderName.OptionN = valueN
```

**3、Layouts**

有时用户希望根据自己的喜好格式化自己的日志输出，Log4j可以在Appenders的后面附加Layouts来完成这个功能。Layouts提供四种日志输出样式，如根据HTML样式、自由指定样式、包含日志级别与信息的样式和包含日志时间、线程、类别等信息的样式。

常使用的类如下：

```
org.apache.log4j.HTMLLayout（以HTML表格形式布局）
org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）
org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等信息）
```

配置模式：

```
log4j.appender.appenderName.layout =className
log4j.appender.appenderName.layout.Option1 = value1
…
log4j.appender.appenderName.layout.OptionN = valueN
```

### 配置详解

在实际应用中，要使Log4j在系统中运行须事先设定配置文件。配置文件事实上也就是对Logger、Appender及Layout进行相应设定。

Log4j支持两种配置文件格式：一种是XML格式的文件，一种是properties属性文件。

下面以properties属性文件为例介绍log4j.properties的配置。

**1、配置根Logger**

```properties
log4j.rootLogger = [ level ] , appenderName1, appenderName2, …
```

**level**：设定日志记录的最低级别，可设的值有OFF、FATAL、ERROR、WARN、INFO、DEBUG、ALL或者自定义的级别，Log4j建议只使用中间四个级别。

通过在这里设定级别，您可以控制应用程序中相应级别的日志信息的开关，比如在这里设定了INFO级别，则应用程序中所有DEBUG级别的日志信息将不会被打印出来。

**appenderName**：就是指定日志信息要输出到哪里。可以同时指定多个输出目的地，用逗号隔开。

例如：log4j.rootLogger＝INFO,A1,B2,C3

```properties
log4j.additivity.org.apache=false
```

表示Logger不会在父Logger的appender里输出，默认为true。

**2、配置日志信息输出目的地（appender）**

```
log4j.appender.appenderName = className
```

**appenderName**：自定义appderName，在log4j.rootLogger设置中使用；

**className**：可设值如下：

- org.apache.log4j.ConsoleAppender

控制台

- org.apache.log4j.FileAppender

文件

- org.apache.log4j.DailyRollingFileAppender

每天产生一个日志文件

- org.apache.log4j.RollingFileAppender

文件大小到达指定尺寸的时候产生一个新的文件

- org.apache.log4j.WriterAppender

将日志信息以流格式发送到任意指定的地方

\1) ConsoleAppender选项

```
Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。
ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。
Target=System.err：默认值是System.out。
```

\2) FileAppender选项

```
Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。
ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。
Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。
File=D:/logs/logging.log4j：指定消息输出到logging.log4j文件中。
```

\3) DailyRollingFileAppender选项

```
Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。
ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。
Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。
File=D:/logs/logging.log4j：指定当前消息输出到logging.log4j文件中。
DatePattern='.'yyyy-MM：每月滚动一次日志文件，即每月产生一个新的日志文件。当前月的日志文件名为logging.log4j，前一个月的日志文件名为logging.log4j.yyyy-MM。
另外，也可以指定按周、天、时、分等来滚动日志文件，对应的格式如下：
1) '.'yyyy-MM：每月
2) '.'yyyy-ww：每周
3) '.'yyyy-MM-dd：每天
4) '.'yyyy-MM-dd-a：每天两次
5) '.'yyyy-MM-dd-HH：每小时
6) '.'yyyy-MM-dd-HH-mm：每分钟
```

\4) RollingFileAppender选项

```
Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。
ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。
Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。
File=D:/logs/logging.log4j：指定消息输出到logging.log4j文件中。
MaxFileSize=100KB：后缀可以是KB, MB 或者GB。在日志文件到达该大小时，将会自动滚动，即将原来的内容移到logging.log4j.1文件中。
MaxBackupIndex=2：指定可以产生的滚动文件的最大数，例如，设为2则可以产生logging.log4j.1，logging.log4j.2两个滚动文件和一个logging.log4j文件。
```

**3、配置日志信息的输出格式（Layout）**

```
log4j.appender.appenderName.layout=className
```

**className**：可设值如下：

- org.apache.log4j.HTMLLayout

以HTML表格形式布局

- org.apache.log4j.PatternLayout

可以灵活地指定布局模式

- org.apache.log4j.SimpleLayout

包含日志信息的级别和信息字符串

- org.apache.log4j.TTCCLayout

包含日志产生的时间、线程、类别等等信息

\1) HTMLLayout选项

```
LocationInfo=true：输出java文件名称和行号，默认值是false。
Title=My Logging： 默认值是Log4J Log Messages。
```

\2) PatternLayout选项

```
ConversionPattern=%m%n：设定以怎样的格式显示消息。
```

### 格式化符号说明

%p：输出日志信息的优先级，即DEBUG，INFO，WARN，ERROR，FATAL。

%d：输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式

%r：输出自应用程序启动到输出该log信息耗费的毫秒数。

%t：输出产生该日志事件的线程名。

%l：输出日志事件的发生位置，相当于%c.%M(%F:%L)的组合，包括类全名、方法、文件名以及在代码中的行数。

%c：输出日志信息所属的类目，通常就是所在类的全名。

%M：输出产生日志信息的方法名。

%F：输出日志消息产生时所在的文件名称。

%L:：输出代码中的行号。

%m:：输出代码中指定的具体日志信息。

%n：输出一个回车换行符，Windows平台为"rn"，Unix平台为"n"。

%x：输出和当前线程相关联的NDC(嵌套诊断环境)，尤其用到像java servlets这样的多客户多线程的应用中。

%%：输出一个"%"字符。

另外，还可以在%与格式字符之间加上修饰符来控制其最小长度、最大长度、和文本的对齐方式。如：

\1) c：指定输出category的名称，最小的长度是20，如果category的名称长度小于20的话，默认的情况下右对齐。

\2) %-20c："-"号表示左对齐。

\3) %.30c：指定输出category的名称，最大的长度是30，如果category的名称长度大于30的话，就会将左边多出的字符截掉，但小于30的话也不会补空格。

### 附：Log4j比较全面的配置

Log4j配置文件实现了输出到控制台、文件、回滚文件、发送日志邮件、输出到数据库日志表、自定义标签等全套功能。

```properties
log4j.rootLogger=DEBUG,console,dailyFile,im
log4j.additivity.org.apache=true
# 控制台(console)
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.ImmediateFlush=true
log4j.appender.console.Target=System.err
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n

# 日志文件(logFile)
log4j.appender.logFile=org.apache.log4j.FileAppender
log4j.appender.logFile.Threshold=DEBUG
log4j.appender.logFile.ImmediateFlush=true
log4j.appender.logFile.Append=true
log4j.appender.logFile.File=D:/logs/log.log4j
log4j.appender.logFile.layout=org.apache.log4j.PatternLayout
log4j.appender.logFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 回滚文件(rollingFile)
log4j.appender.rollingFile=org.apache.log4j.RollingFileAppender
log4j.appender.rollingFile.Threshold=DEBUG
log4j.appender.rollingFile.ImmediateFlush=true
log4j.appender.rollingFile.Append=true
log4j.appender.rollingFile.File=D:/logs/log.log4j
log4j.appender.rollingFile.MaxFileSize=200KB
log4j.appender.rollingFile.MaxBackupIndex=50
log4j.appender.rollingFile.layout=org.apache.log4j.PatternLayout
log4j.appender.rollingFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 定期回滚日志文件(dailyFile)
log4j.appender.dailyFile=org.apache.log4j.DailyRollingFileAppender
log4j.appender.dailyFile.Threshold=DEBUG
log4j.appender.dailyFile.ImmediateFlush=true
log4j.appender.dailyFile.Append=true
log4j.appender.dailyFile.File=D:/logs/log.log4j
log4j.appender.dailyFile.DatePattern='.'yyyy-MM-dd
log4j.appender.dailyFile.layout=org.apache.log4j.PatternLayout
log4j.appender.dailyFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 应用于socket
log4j.appender.socket=org.apache.log4j.RollingFileAppender
log4j.appender.socket.RemoteHost=localhost
log4j.appender.socket.Port=5001
log4j.appender.socket.LocationInfo=true
# Set up for Log Factor 5
log4j.appender.socket.layout=org.apache.log4j.PatternLayout
log4j.appender.socket.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# Log Factor 5 Appender
log4j.appender.LF5_APPENDER=org.apache.log4j.lf5.LF5Appender
log4j.appender.LF5_APPENDER.MaxNumberOfRecords=2000
# 发送日志到指定邮件
log4j.appender.mail=org.apache.log4j.net.SMTPAppender
log4j.appender.mail.Threshold=FATAL
log4j.appender.mail.BufferSize=10
log4j.appender.mail.From = xxx@mail.com
log4j.appender.mail.SMTPHost=mail.com
log4j.appender.mail.Subject=Log4J Message
log4j.appender.mail.To= xxx@mail.com
log4j.appender.mail.layout=org.apache.log4j.PatternLayout
log4j.appender.mail.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 应用于数据库
log4j.appender.database=org.apache.log4j.jdbc.JDBCAppender
log4j.appender.database.URL=jdbc:mysql://localhost:3306/test
log4j.appender.database.driver=com.mysql.jdbc.Driver
log4j.appender.database.user=root
log4j.appender.database.password=
log4j.appender.database.sql=INSERT INTO LOG4J (Message) VALUES('=[%-5p] %d(%r) --> [%t] %l: %m %x %n')
log4j.appender.database.layout=org.apache.log4j.PatternLayout
log4j.appender.database.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n

# 自定义Appender
log4j.appender.im = net.cybercorlin.util.logger.appender.IMAppender
log4j.appender.im.host = mail.cybercorlin.net
log4j.appender.im.username = username
log4j.appender.im.password = password
log4j.appender.im.recipient = corlin@cybercorlin.net
log4j.appender.im.layout=org.apache.log4j.PatternLayout
log4j.appender.im.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
```

Log4j的强大功能无可置疑，但实际应用中免不了遇到某个功能需要输出独立的日志文件的情况，怎样才能把所需的内容从原有日志中分离，形成单独的日志文件呢？其实只要在现有的Log4j基础上稍加配置即可轻松实现这一功能。

先看一个常见的log4j.properties文件，它是在控制台和myweb.log文件中记录日志：

```properties
log4j.rootLogger=DEBUG, stdout, logfile

log4j.category.org.springframework=ERROR
log4j.category.org.apache=INFO

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n

log4j.appender.logfile=org.apache.log4j.RollingFileAppender
log4j.appender.logfile.File=${myweb.root}/WEB-INF/log/myweb.log
log4j.appender.logfile.MaxFileSize=512KB
log4j.appender.logfile.MaxBackupIndex=5
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n
```

如果想对不同的类输出不同的文件(以cn.com.Test为例)，先要在Test.java中定义:

```properties
private static Log logger = LogFactory.getLog(Test.class);
```

然后在log4j.properties中加入:

```properties
log4j.logger.cn.com.Test= DEBUG, test

log4j.appender.test=org.apache.log4j.FileAppender
log4j.appender.test.File=${myweb.root}/WEB-INF/log/test.log
log4j.appender.test.layout=org.apache.log4j.PatternLayout
log4j.appender.test.layout.ConversionPattern=%d %p [%c] - %m%n
```

也就是让cn.com.Test中的logger使用log4j.appender.test所做的配置。

但是，如果在同一类中需要输出多个日志文件呢？其实道理是一样的，先在Test.java中定义:

```properties
private static Log logger1 = LogFactory.getLog("myTest1");
private static Log logger2 = LogFactory.getLog("myTest2");
```

然后在log4j.properties中加入:

```properties
log4j.logger.myTest1= DEBUG, test1

log4j.appender.test1=org.apache.log4j.FileAppender
log4j.appender.test1.File=${myweb.root}/WEB-INF/log/test1.log
log4j.appender.test1.layout=org.apache.log4j.PatternLayout
log4j.appender.test1.layout.ConversionPattern=%d %p [%c] - %m%n

log4j.logger.myTest2= DEBUG, test2

log4j.appender.test2=org.apache.log4j.FileAppender
log4j.appender.test2.File=${myweb.root}/WEB-INF/log/test2.log
log4j.appender.test2.layout=org.apache.log4j.PatternLayout
log4j.appender.test2.layout.ConversionPattern=%d %p [%c] - %m%n
```

也就是在用logger时给它一个自定义的名字(如这里的"myTest1")，然后在log4j.properties中做出相应配置即可。别忘了不同日志要使用不同的logger(如输出到test1.log的要用logger1.info("abc"))。

还有一个问题，就是这些自定义的日志默认是同时输出到log4j.rootLogger所配置的日志中的，如何能只让它们输出到自己指定的日志中呢？别急，这里有个开关：

```
log4j.additivity.myTest1 = false
```

它用来设置是否同时输出到log4j.rootLogger所配置的日志中，设为false就不会输出到其它地方啦！注意这里的"myTest1"是你在程序中给logger起的那个自定义的名字！

如果你说，我只是不想同时输出这个日志到log4j.rootLogger所配置的logfile中，stdout里我还想同时输出呢！那也好办，把你的log4j.logger.myTest1 = DEBUG, test1改为下式就OK啦！

```properties
log4j.logger.myTest1=DEBUG, stdout, test1
```

下面是文件上传时记录文件类型的log日志，并输出到指定文件的配置

```properties
log4j.rootLogger=INFO, stdout
######################### logger ##############################

log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.conversionPattern = %d [%t] %-5p %c - %m%n
log4j.logger.extProfile=INFO, extProfile#日志级别是INFO,标签是extProfile
log4j.additivity.extProfile=false;#输出到指定文件extProfile.log中

#userProfile log\uff08\u8bb0\u5f55\u4fee\u6539\u5bc6\u7801\uff0c\u627e\u56de\u5bc6\u7801\uff0c\u4fee\u6539\u90ae\u7bb1\uff0c\u4fee\u6539\u624b\u673a\u53f7\uff09
log4j.appender.extProfile=org.apache.log4j.RollingFileAppender
log4j.appender.extProfile.File=logs/extProfile.log#输出到resin根目录的logs文件夹,log4j会自动生成目录和文件
log4j.appender.extProfile.MaxFileSize=20480KB#超过20M就重新创建一个文件
log4j.appender.extProfile.MaxBackupIndex=10
log4j.appender.extProfile.layout=org.apache.log4j.PatternLayout
log4j.appender.extProfile.layout.ConversionPattern=%d [%t] %-5p %c - %m%n
```

Java端控制代码

```java
<%@page contentType="text/html" session="false" pageEncoding="UTF-8"%><%@page
...
org.apache.commons.logging.Log,
org.apache.commons.logging.LogFactory
"%>
...
Log extProfile  = LogFactory.getLog("extProfile");
...
if (!item.isFormField()) {
       String fileExt = StringUtils.substringAfterLast(item.getName(), ".").toLowerCase();
       extProfile.info("upfile type is : [ "+fileExt +" ]");
}
```

 



### SpringBoot

Spring Boot 使用 `Apache Commons Logging` 作为内部的日志框架门面，它只是一个日志接口，在实际应用中需要为该接口来指定相应的日志实现。

Spring Boot 默认的日志实现是 `Logback`

在 Spring Boot 项目中，只要添加了如下 web 依赖，日志依赖就自动添加进来了：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**日志配置**

Spring Boot 的日志系统会自动根据 classpath 下的内容选择合适的日志配置，在这个过程中首选 Logback。

如果开发者需要修改日志级别，只需要在 application.properties 文件中通过 `logging.level 前缀+包名` 的形式进行配置即可

```properties
logging.level.org.springframework.web=debug
logging.level.org.hibernate=error
```

如果你想将日志输出到文件，可以通过如下配置指定日志文件名：

```properties
logging.file.name=javaboy.log
或者
logging.file.name=/Users/sang/Documents/javaboy/javaboy.log
```

指定日志文件全路径

```properties
logging.file.path=/Users/sang/Documents/javaboy
```

想对输出到文件中的日志进行精细化管理，还有如下一些属性可以配置：

```properties
logging.logback.rollingpolicy.file-name-pattern：日志归档的文件名，日志文件达到一定大小之后，自动进行压缩归档。
logging.logback.rollingpolicy.clean-history-on-start：是否在应用启动时进行归档管理。
logging.logback.rollingpolicy.max-file-size：日志文件大小上限，达到该上限后，会自动压缩。
logging.logback.rollingpolicy.total-size-cap：日志文件被删除之前，可以容纳的最大大小。
logging.logback.rollingpolicy.max-history：日志文件保存的天数。
```

### Log4j 配置

如果 classpath 下存在 `Log4j2` 的依赖，Spring Boot 会自动进行配置。

默认情况下 classpath 下当然不存在 `Log4j2` 的依赖，如果想使用 `Log4j2`，可以排除已有的 `Logback`，然后再引入 `Log4j2`，如下：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

`Log4j2` 的配置就比较容易了，在 reources 目录下新建 log4j2.xml 文件，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration status="warn">
    <properties>
        <Property name="app_name">logging</Property>
        <Property name="log_path">logs/${app_name}</Property>
    </properties>
    <appenders>
        <console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="[%d][%t][%p][%l] %m%n" />
        </console>
        <RollingFile name="RollingFileInfo" fileName="${log_path}/info.log"
                     filePattern="${log_path}/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <ThresholdFilter level="INFO" />
                <ThresholdFilter level="WARN" onMatch="DENY"
                                 onMismatch="NEUTRAL" />
            </Filters>
            <PatternLayout pattern="[%d][%t][%p][%c:%L] %m%n" />
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true" />
                <SizeBasedTriggeringPolicy size="2 MB" />
            </Policies>
            <DefaultRolloverStrategy compressionLevel="0" max="10"/>
        </RollingFile>
        <RollingFile name="RollingFileWarn" fileName="${log_path}/warn.log"
                     filePattern="${log_path}/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <ThresholdFilter level="WARN" />
                <ThresholdFilter level="ERROR" onMatch="DENY"
                                 onMismatch="NEUTRAL" />
            </Filters>
            <PatternLayout pattern="[%d][%t][%p][%c:%L] %m%n" />
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true" />
                <SizeBasedTriggeringPolicy size="2 MB" />
            </Policies>
            <DefaultRolloverStrategy compressionLevel="0" max="10"/>
        </RollingFile>

        <RollingFile name="RollingFileError" fileName="${log_path}/error.log"
                     filePattern="${log_path}/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log.gz">
            <ThresholdFilter level="ERROR" />
            <PatternLayout pattern="[%d][%t][%p][%c:%L] %m%n" />
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true" />
                <SizeBasedTriggeringPolicy size="2 MB" />
            </Policies>
            <DefaultRolloverStrategy compressionLevel="0" max="10"/>
        </RollingFile>
    </appenders>
    <loggers>
        <root level="info">
            <appender-ref ref="Console" />
            <appender-ref ref="RollingFileInfo" />
            <appender-ref ref="RollingFileWarn" />
            <appender-ref ref="RollingFileError" />
        </root>
    </loggers>
</configuration>
```

首先在 properties 节点中指定了应用名称以及日志文件位置。

然后通过几个不同的 RollingFile 对不同级别的日志分别处理，不同级别的日志将输出到不同的文件，并按照各自的命名方式进行压缩。

### logback

**对比 log4j 的优点**：

- 内核重写，实现更快，初始化内存加载更小。

- 充分的测试。
- ogback-classic 非常自然实现了 SLF4j，容易切换。
- 自动重新加载配置文件

**logback.xml 常用配置详解**

(1) 根节点<configuration>，包含下面三个属性：

scan: 当此属性设置为 true 时，配置文件如果发生改变，将会被重新加载，默认值为 true。

scanPeriod: 设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当 scan 为 true 时，此属性生效。默认的时间间隔为 1 分钟。

debug: 当此属性设置为 true 时，将打印出 logback 内部日志信息，实时查看 logback 运行状态。默认值为 false。

例如：

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">
　　  <!--其他配置省略-->
</configuration>　
```

(2) 子节点<contextName>：用来设置上下文名称，每个 logger 都关联到 logger 上下文，默认上下文名称为 default。但可以使用<contextName>设置成其他名字，用于区分不同应用程序的记录。一旦设置，不能修改。

例如：

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">
     <contextName>myAppName</contextName>
　　  <!--其他配置省略-->
</configuration>
```

(3) 子节点<property> ：用来定义变量值，它有两个属性 name 和 value，通过<property>定义的值会被插入到 logger 上下文中，可以使“${}”来使用变量。name: 变量的名称 value: 的值时变量定义的值
　　例如：

```
<configuration scan="true" scanPeriod="60 seconds" debug="false">
　　　<property name="APP_Name" value="myAppName" />
　　　<contextName>${APP_Name}</contextName>
　　　<!--其他配置省略-->
　　　
　　　<!--等级，文件路径，格式-->
　　　
　　　
</configuration>
```

(4) 子节点<timestamp>：获取时间戳字符串，他有两个属性 key 和 datePattern

key: 标识此<timestamp> 的名字；

datePattern: 设置将当前时间（解析配置文件的时间）转换为字符串的模式，遵循 java.txt.SimpleDateFormat 的格式。
　　例如：

```
<configuration scan="true" scanPeriod="60 seconds" debug="false">
　　　　　　<timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/>
　　　　　　<contextName>${bySecond}</contextName>
　　　　　　<!-- 其他配置省略-->
</configuration>
```

(5) 子节点<appender>：负责写日志的组件，它有两个必要属性 name 和 class。name 指定 appender 名称，class 指定 appender 的全限定名

```xml
<appender namas="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
        <pattern></pattern>
    </encoder>
</appender>
```



（6）子节点<loger>：用来设置某一个包或具体的某一个类的日志打印级别、以及指定<appender>。<loger>仅有一个 name 属性，一个可选的 level 和一个可选的 addtivity 属性。可以包含零个或多个<appender-ref>元素，标识这个 appender 将会添加到这个 loger，name: 用来指定受此 loger 约束的某一个包或者具体的某一个类。level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，还有一个特俗值 INHERITED 或者同义词 NULL，代表强制执行上级的级别。 如果未设置此属性，那么当前 loger 将会继承上级的级别。addtivity: 是否向上级 loger 传递打印信息。默认是 true。同<loger>一样，可以包含零个或多个<appender-ref>元素，标识这个 appender 将会添加到这个 loger。

（7）子节点<root>:它也是<loger>元素，但是它是根 loger,是所有<loger>的上级。只有一个 level 属性，因为 name 已经被命名为"root",且已经是最上级了。level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，不能设置为 INHERITED 或者同义词 NULL。 默认是 DEBUG。



Logger：日志记录器

Appender：指定日志输出的目的地

Layout：负责把时间转换成字符串，输出格式化的日志信息



## 项目出现问题怎么排查日志

1、能通过 less 命令打开文件，通过 Shift+G 到达文件底部，再通过?+关键字的方式来根据关键来搜索信息。

2、能通过 grep 的方式查关键字，具体用法是, grep 关键字 文件名，如果要两次在结果里查找的话，就用 grep 关键字 1 文件名 | 关键字 2 --color。最后--color 是高亮关键字。

3、能通过 vi 来编辑文件。
