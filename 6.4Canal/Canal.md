阿里开源出的一个基于Mysql的日志增量变更的订阅消费组件。

使用情景：

1：实时监测某张表的变化然后作出相应的处理

2：实时更新某张表的变化然后更新至Redis服务器



## 工作原理

首先看主从复制的原理：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190824095308386.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyMDU0Mg==,size_16,color_FFFFFF,t_70)

MySQL master 将数据变更写入二进制日志( binary log, 其中记录叫做二进制日志事件binary log events，可以通过 show binlog events 进行查看)
MySQL slave 将 master 的 binary log events 拷贝到它的中继日志(relay log)
MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据

再看canal的原理：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190824101348518.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzgyMDU0Mg==,size_16,color_FFFFFF,t_70)

canal 模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送dump 协议
MySQL master 收到 dump 请求，开始推送 binary log 给 slave (即 canal )
canal 解析 binary log 对象(原始为 byte 流)

获取主库的变化后，再通过两种途径传递给程序，一种途径就是他自身支持的TCP连接，程序直接与Canal建立一个TCP连接；第二种就是Canal将接受到的数据插入到消息中间件中去，程序再重消息中间件中拿到这些数据，

## 使用

下载：https://github.com/alibaba/canal/releases

直接下载deploy这个包；

下载后解压：

目录有：

bin

conf

lib

logs



## 配置mysql

https://github.com/alibaba/canal/wiki/QuickStart

1：开启mysql的binlog

对于自建 MySQL , 需要先开启 Binlog 写入功能，配置 binlog-format 为 ROW 模式，my.cnf 中配置如下

```properties
[mysqld]
log-bin=mysql-bin # 开启 binlog
binlog-format=ROW # 选择 ROW 模式
server_id=1 # 配置 MySQL replaction 需要定义，不要和 canal 的 slaveId 重复
```

2：为canal添加一些权限

canal模拟成mysql的一个从库；

```sql
CREATE USER canal IDENTIFIED BY 'canal';  
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'%';
-- GRANT ALL PRIVILEGES ON *.* TO 'canal'@'%' ;
FLUSH PRIVILEGES;
```

验证用户添加成功；

```sql
select * from mysql.user where User="canal" \G
```

修改myCat配置文件



3：修改canal相关配置文件

`canal.deployer-1.1.5\conf\example\instance.properties` 进行局部实例配置，可以修改数据库账号和密码、数据库表名、binlog 文件名和 position 等

```txt
# 没有改变的就没有贴出来，注意 MySQL 的用户名和密码
canal.instance.master.address=192.168.58.131:3306
# username/password
canal.instance.dbUsername=test
canal.instance.dbPassword=liubihao
canal.instance.connectionCharset = UTF-8
# enable druid Decrypt database password
canal.instance.enableDruid=false
# table regex
canal.instance.filter.regex=.*\\..*
# table black regex
canal.instance.filter.black.regex=
```

4：处理

```java
import com.alibaba.otter.canal.client.CanalConnector;
import com.alibaba.otter.canal.client.CanalConnectors;
import com.alibaba.otter.canal.protocol.CanalEntry.*;
import com.alibaba.otter.canal.protocol.Message;
import org.springframework.stereotype.Component;

import java.util.List;
import java.net.InetSocketAddress;

@Component
public class CanalClient {
  private static void printEntries(List<Entry> entries) throws Exception {
    for (Entry entry : entries) {
      if (entry.getEntryType() != EntryType.ROWDATA) {
        continue;
      }

      RowChange rowChange = RowChange.parseFrom(entry.getStoreValue());

      EventType eventType = rowChange.getEventType();
      System.out.println(String.format("================> binlog[%s:%s] , name[%s,%s] , eventType : %s",
                                       entry.getHeader().getLogfileName(), entry.getHeader().getLogfileOffset(),
                                       entry.getHeader().getSchemaName(), entry.getHeader().getTableName(), eventType));

      for (RowData rowData : rowChange.getRowDatasList()) {
        switch (rowChange.getEventType()) {
          case INSERT:
            System.out.println("INSERT ");
            printColumns(rowData.getAfterColumnsList());
            break;
          case UPDATE:
            System.out.println("UPDATE ");
            printColumns(rowData.getAfterColumnsList());
            break;
          case DELETE:
            System.out.println("DELETE ");
            printColumns(rowData.getBeforeColumnsList());
            break;
          default:
            break;
        }
      }
    }
  }

  private static void printColumns(List<Column> columns) {
    for (Column column : columns) {
      System.out.println(column.getName() + " : " + column.getValue() + " update=" + column.getUpdated());
    }
  }

  public static void main(String[] args) throws Exception {
    // hostname, port, destination, username, password
    CanalConnector connector = CanalConnectors.newSingleConnector(new InetSocketAddress("127.0.0.1", 11111), "example", "", "");
    try {
      connector.connect();
      // 监听的表，格式为数据库.表名,数据库.表名
      connector.subscribe(".*\\..*");
      connector.rollback();

      while (true) {
        Message message = connector.getWithoutAck(100); // 获取指定数量的数据
        long batchId = message.getId();
        if (batchId == -1 || message.getEntries().isEmpty()) {
          Thread.sleep(1000);
          continue;
        }
        // System.out.println(message.getEntries());
        printEntries(message.getEntries());
        connector.ack(batchId); // 提交确认，消费成功，通知server删除数据
        // connector.rollback(batchId);// 处理失败, 回滚数据，后续重新获取数据
      }
    } catch (Exception e) {
      System.out.println("Something Error.");
    } finally {
      connector.disconnect();
    }
  }
}
```





## 验证

https://github.com/alibaba/canal/wiki/ClientExample







