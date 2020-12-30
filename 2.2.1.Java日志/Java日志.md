本文的目的是搞清楚Java中各种日志Log之间是怎么的关系，如何作用、依赖，好让我们平时在工作中如果遇到“日志打不出”或者“日志jar包冲突”等之类的问题知道该如何入手解决，以及在各种场景下如何调整项目中的各个框架的日志输出，使得输出统一。



在日常工作中我们可能看到项目中依赖的跟日志相关的jar包有很多，`commons-logging.jar`、`log4j.jar`、`sl4j-api.jar`、`logback.jar`等等，眼花缭乱。我们要正确的配置，使得jar包相互作用生效之前，就先要理清它们之间的关系。



发展史：

那就要从Java Log的发展历程开始说起。

1. `log4j`（作者Ceki Gülcü）出来时就等到了广泛的应用（注意这里是直接使用），是Java日志事实上的标准，并成为了Apache的项目
2. Apache要求把log4j并入到JDK，SUN拒绝，并在jdk1.4版本后增加了`JUL`（`java.util.logging`）
3. 毕竟是JDK自带的，JUL也有很多人用。同时还有其他日志组件，如SimpleLog等。这时如果有人想换成其他日志组件，如log4j换成JUL，因为api完全不同，就需要改动代码。
4. Apache见此，开发了`JCL`（Jakarta Commons Logging），即`commons-logging-xx.jar`。它只提供一套通用的日志接口api，并不提供日志的实现。很好的设计原则嘛，依赖抽象而非实现。这样应用程序可以在运行时选择自己想要的日志实现组件。
5. 这样看上去也挺美好的，但是log4j的作者觉得JCL不好用，自己开发出`slf4j`，它跟JCL类似，本身不替供日志具体实现，只对外提供接口或门面。目的就是为了替代JCL。同时，还开发出`logback`，一个比log4j拥有更高性能的组件，目的是为了替代log4j。
6. Apache参考了logback,并做了一系列优化，推出了`log4j2`



JCL

`commons-logging`已经停止更新，最后的状态如下所示：

![image-20201229144640097](media/image-20201229144640097.png)





SLF4j

意思为如果你想用slf4j作为日志门面的话，你如何去配合使用其他日志实现组件，这里说明一下（注意jar包名缺少了版本号，在找版本时也要注意版本之间是否兼容）

- slf4j + logback`slf4j-api.jar` + `logback-classic.jar` + `logback-core.jar`
- slf4j + log4j`slf4j-api.jar` + `slf4j-log4j12.jar` + `log4j.jar`
- slf4j + jul`slf4j-api.jar` + `slf4j-jdk14.jar`
- 也可以只用slf4j无日志实现`slf4j-api.jar` + `slf4j-nop.jar`



Spring

Spring是用JCL作为日志门面的

