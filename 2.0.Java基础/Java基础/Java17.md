# Java12

2019 年 3 月 20 日

## 1：Switch 表达式

缺点：

忘了 break 都会执行，

在各个 case 中定义的局部变量名不可重复

新的 switch 表达式

可以返回值

```java
switch(fruit){
        //不再写break，直接跳
        case PEAR->System.out.println("1");
        //多个一样的可以放在一起
        case APPLE,GRAPE,MANGO->System.out.println("2");
        default->new IllegalStateException("No Such Fruit");
}
```

## 2：shenandoah GC 低停顿时间的 GC

省得我

与堆大小无关，吞吐量有限的原则

新生代内存空间较小，所以暂停的时间可以接受，但是一旦老年代出现 FullGC，线程暂停的时间就会更久

工作原理：

其内存结构与 G1 非常相似，都是将内存划分为类似棋盘的 region。整体流程与 G1 也是比较相似的，最大的区别在于实现了并发的 疏散(Evacuation) 环节，引入的 BrooksForwarding Pointer 技术使得 GC 在移动对象时，对象引用仍然可以访问。

Shenandoah GC 工作周期如下所示：

![image-20201205193344731](media/image-20201205193344731.png)

上图对应工作周期如下：

1. Init Mark 启动并发标记 阶段
2. 并发标记遍历堆阶段
3. 并发标记完成阶段
4. 并发整理回收无活动区域阶段
5. 并发 Evacuation 整理内存区域阶段
6. Init Update Refs 更新引用初始化 阶段
7. 并发更新引用阶段
8. Final Update Refs 完成引用更新阶段
9. 并发回收无引用区域阶段

推荐几个配置或调试 Shenandoah 的 JVM 参数:

```xml
-XX:+AlwaysPreTouch：使用所有可用的内存分页，减少系统运行停顿，为避免运行时性能损失。
-Xmx == -Xmsv：设置初始堆大小与最大值一致，可以减轻伸缩堆大小带来的压力，与 AlwaysPreTouch 参数配
合使用，在启动时提交所有内存，避免在最终使用中出现系统停顿。
-XX:+ UseTransparentHugePages：能够大大提高大堆的性能，同时建议在 Linux 上使用时将
/sys/kernel/mm/transparent_hugepage/enabled 和
/sys/kernel/mm/transparent_hugepage/defragv 设置为：madvise，同时与 AlwaysPreTouch 一起使
用时，init 和 shutdownv 速度会更快，因为它将使用更大的页面进行预处理。
-XX:+UseNUMA：虽然 Shenandoah 尚未明确支持 NUMA（Non-Uniform Memory Access），但最好启用此功
能以在多插槽主机上启用 NUMA 交错。与 AlwaysPreTouch 相结合，它提供了比默认配置更好的性能。
-XX:+DisableExplicitGC：忽略代码中的 System.gc() 调用。当用户在代码中调用 System.gc() 时会强制
Shenandoah 执行 STW Full GC ，应禁用它以防止执行此操作，另外还可以使用 -
XX:+ExplicitGCInvokesConcurrent，在 调用 System.gc() 时执行 CMS GC 而不是 Full GC，建议在有
System.gc() 调用的情况下使用。
不过目前 Shenandoah 垃圾回收器还被标记为实验项目，如果要使用Shenandoah GC需要编译时--with-jvmfeatures
选项带有shenandoahgc，然后启动时使用参数
-XX:+UnlockExperimentalVMOptions -XX:+UseShenandoahGC
```

## 3：常量 API

## 4：微基准测试套件

JMH，即 Java Microbenchmark Harness，是专门用于代码微基准测试的工具套件。何谓 Micro Benchmark 呢？简
单的来说就是基于方法层面的基准测试，精度可以达到微秒级。当你定位到热点方法，希望进一步优化方法性能的时
候，就可以使用 JMH 对优化的结果进行量化的分析。
JMH 比较典型的应用场景：
想准确的知道某个方法需要执行多长时间，以及执行时间和输入之间的相关性；
对比接口不同实现在给定条件下的吞吐量；
查看多少百分比的请求在多长时间内完成；

JMH 的使用
要使用 JMH，首先需要准备好 Maven 环境，JMH 的源代码以及官方提供的 Sample 就是使用 Maven 进行项目管理的，
github 上也有使用 gradle 的例子可自行搜索参考。使用 mvn 命令行创建一个 JMH 工程：
如果要在现有 Maven 项目中使用 JMH

```cmd
mvn archetype:generate \
        -DinteractiveMode=false \
        -DarchetypeGroupId=org.openjdk.jmh \
        -DarchetypeArtifactId=jmh-java-benchmark-archetype \
        -DgroupId=co.speedar.infra \
        -DartifactId=jmh-test \
        -Dversion=1.0
```

只需要把生成出来的两个依赖以及 shade 插件拷贝到项目的 pom 中即可：

## 5：只保留一个 AArch64 实现

Java 12 中将删除由 Oracle 提供的 arm64 端口相关的所有源码，即删除目录 open/src/hotspot/cpu/arm 中关于
64-bit 的这套实现，只保留其中有关 32-bit ARM 端口的实现，余下目录的 open/src/hotspot/cpu/aarch64 代码
部分就成了 AArch64 的默认实现。这将使开发贡献者将他们的精力集中在单个 64 位 ARM 实现上，并消除维护两套实现所需的重复工作。

## 6：默认生成类数据共享(CDS)归档文件

## 7：可中断的 G1 Mixed GC

G1 垃圾收集器设计的主要目标之一是满足用户设置的预期的 JVM 停顿时间

G1 收集器必须完成收集集合的所有区域中的所有活动对象之后才能停止；但是如果收集器选择过大的 GC 回收集，此时的 STW 时间会过长超出目标 pause time。

将 GC 回收集拆分为必需和可选部分时，垃圾收集过程优先处理必需部分。同时，需要为可选 GC 回收集部分维护一些其他数据，这会产生轻微的 CPU 开销，但小于 1 ％的变化，同时在 G1 回收器处理 GC 回收集期间，本机内存使用率也可能会增加，使用上述情况只适用于包含可选 GC 回收部分的 GC 混合回收集合。在 G1 垃圾回收器完成收集需要必需回收的部分之后，如果还有时间的话，便开始收集可选的部分。但是粗粒度的处理，可选部分的处理粒度取决于剩余的时间，一次只能处理可选部分的一个子集区域。在完成可选收集部
分的收集后，G1 垃圾回收器可以根据剩余时间决定是否停止收集。如果在处理完必需处理的部分后，剩余时间不足，总时间花销接近预期时间，G1 垃圾回收器也可以中止可选部分的回收以达到满足预期停顿时间的目标。

## 8：增强 G1，自动返回未用堆内存给操作系统

# Java13

## 1：switch 表达式增加 yield

用于返回值

```java
String x = "3";
int i = switch (x) {
	case "1" -> 1;
	case "2" -> 2;
	default -> {
		yield 3;
	}
};
System.out.println(i);
```

## 2：文本块

对于一些 html 文本或则 sql 语句，拼接四问题

```java
//原来的html拼接
String html = "<html>\n" +
                " <body>\n" +
                " <p>Hello, World</p>\n" +
                " </body>\n" +
                "</html>\n";
//原本的SQL
String query = "select employee_id,last_name,salary,department_id\n" +
                "from employees\n" +
                "where department_id in (40,50,60)\n" +
                "order by department_id asc";
//JDK13html拼接
String html1 = """
                <html>
                <body>
                <p>Hello, world</p>
                </body>
                </html>
			  """;
//JDK13的SQL拼接
String newQuery = """
                select employee_id,last_name,salary,department_id
                from employees
                where department_id in (40,50,60)
                order by department_id asc
				""";
```

开始分隔符是由三个双引号字符（"""），后面可以跟零个或多个空格，最终以行终止符结束。文本块内容
以开始分隔符的行终止符后的第一个字符开始。
结束分隔符也是由三个双引号字符（"""）表示，文本块内容以结束分隔符的第一个双引号之前的最后一个
字符结束。

```java
public void test(){
    //以开始分隔符的行终止符后的第一个字符开始
    //以结束分割符的第一个双引号之前的最后一个字符结束
    String text1 = """
        abc""";
    String text2 = "abc";
    System.out.println(text1==text2);
    
    String text3 = """
        abc
        """;
        System.out.println(text1.length());//3
    System.out.print(text3.length());//4
    
}
```

注意：

编译器在编译时会删除掉这些多余的空格。不过行前有没有空格在于终止符的位置

转义字符

```java
//错误
String d = """
			abc \ def
			"""; // 含有未转义的反斜线
```

文本框连接

```java
String code = """
        public void print(""" + type + """
            o) {
            	System.out.println(Objects.toString(o));
            }
		""";
```

更简洁的替代方法是使用 String :: replace 或 String :: format，比如：

```java
String code = """
        public void print($type o) {
        	System.out.println(Objects.toString(o));
        }
		""".replace("$type", type);
String code = String.format("""
        public void print(%s o) {
        	System.out.println(Objects.toString(o));
        }
		""", type);
```

另一个方法是使用 String :: formatted，这是一个新方法，比如：

```java
String source = """
        public void print(%s object) {
        	System.out.println(Objects.toString(object));
        }
		""".formatted(type);
```

## 3：动态 CDS 档案（动态类数据共享归档）

## 4：ZGC:取消使用未使用的内存

## 5：重新实现旧版套接字 API

# Java17

