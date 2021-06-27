JMX 全称为 Java Management Extensions 。 Java 管理扩展，用来管理和监测 Java 程序。最常用到的就是对于 JVM 的监测和管理，比如 JVM 内存、CPU 使用率、线程数、垃圾收集情况等等。另外，还可以用作日志级别的动态修改，比如 log4j 就支持 JMX 方式动态修改线上服务的日志级别。最主要的还是被用来做各种监控工具，比如文章开头提到的 Spring Boot Actuator、JConsole、VisualVM 等。

## MBean

JMX 是通过各种 MBean(Managed Bean) 传递消息的，MBean 其实就是我们经常说的 Java Bean，只不过由于它比较特殊，所以称之为 MBean。

