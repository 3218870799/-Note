# 各版本区别：

mysql8相较于mysql5.7：

1：性能提升;

2：Nosql文档支持

3：窗口函数

1：备份

使用Percona XtraBackup备份必须使用Percona XtraBackup8.0.12以上的版本，命令也发生了改变；

2：隐藏索引

在8中，索引可以被隐藏和显示，主要使用在性能调试中，如果一个索引被隐藏，它就不会被查询优化器使用。

也就是说可以隐藏一个索引,然后观察对数据库的影响.如果性能下降,就说明这个索引是有效的,于是将其”恢复显示”即可;如果数据库性能看不出变化,说明这个索引是多于的,可以删掉了。

隐藏一个索引的语法

```sql
ALTER TABLE t ALTER INDEX i INVISIBLE;
```

恢复显示该索引的语法是：

```sql
ALTER TABLE t ALTER INDEX i VISIBLE;
```

3：utf8编码

众所周知，mysql以前的utf8编码并不是真正的utf8编码，只有utf8mb4才是真正的utf8编码，mysql8将默认编码设置成了utf8mb4



# 版本升级

1：项目中mysql-connector-java.jar的版本改为对应的数据库版本；

2：备份mysql数据

3：备份数据库

4：检查所有表是否与当前版本兼容



# 版本问题

1：MySQL5.7.5后only_full_group_by成为sql_mode的默认选项之一，这可能导致一些sql语句失效。比如在使用group by进行分组查询报错

解决办法：

将only_full_group_by去掉；

```sql
SHOW SESSION VARIABLES; 
set session sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'; 
SHOW GLOBAL VARIABLES;

set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

2：数据结构迁移

mysql5.7不能给日期赋予0值，而你在建表的时候它默认又为0000-00-00 00:00:00，

解决：



