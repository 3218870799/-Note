Java模板引擎：

# 基本语法

```velocity

#set( $treeList = ["pine", "oak", "maple", "redwood"]) ## 设置变量

#foreach ($name in $treeList)
#end

#liternal() ….#end ## 原样输出
```

包含

```velocity
#include("a.vm")
#include("b.txt")
#parse("c.vm")
#include( "one.gif","two.txt","three.htm" )
 #include( "greetings.txt", $seasonalstock )
```

include包含的文件不会被解析，原样显示。

parse包含的文件会解析。

是否解析跟包含文件扩展名无关，只跟include还是parse命令有关

停止

stop之后内容停止输出

```velocity
#stop
$a
```

宏调用

```velocity
#macro(d $a $b)
    hello,$a,$b
#end

#d("zhangsan", "lisi")
输出：
hello,zhangsan,lisi
```

转义：

