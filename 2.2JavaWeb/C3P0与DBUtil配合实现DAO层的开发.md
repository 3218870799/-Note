写在前面：菜鸟拙见，望请纠正

## 一：为什么需要连接池　

普通的JDBC数据库连接使用 DriverManager 来获取，每次向数据库建立连接的时候都要将 Connection 加载到内存中，需要数据库连接的时候，就向数据库要求一个，执行完成后再断开连接。。这样的方式将会消耗大量的资源和时间

数据库连接池的基本思想就是为数据库连接建立一个“缓冲池”。预先在缓冲池中放入一定数量的连接，当需要建立数据库连接时，只需从“缓冲池”中取出一个，使用完毕之后再放回去。

![img](http://images2017.cnblogs.com/blog/1174906/201710/1174906-20171022160046084-846523908.png)

其实就是以前的时候每一次执行操作都要获得一个conn连接，用完就断掉，而现在，我可以一开始就创建多个连接，存放到一个连接池里，等到用的时候我就从连接池里取一个，用完只是断开与连接池的连接而已，而连接池与数据库的连接却不会断，会重复使用conn来提高效率

## 二：怎么用连接池

1：导包

2：配置C3P0：在src目录下新建一个名叫c3p0-config.xml的文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
   <!--默认配置-->
    <default-config> 
  <!--初始化时连接池建立的连接数--> 
        <property name="initialPoolSize">10</property>
<!--每个连接的生存时间-->  
        <property name="maxIdleTime">30</property>
<!--连接池最多和最少容纳的连接-->  
        <property name="maxPoolSize">100</property>  
        <property name="minPoolSize">10</property>
  
        <property name="maxStatements">200</property>  
    </default-config>  
   <!--配置连接池mysql-->
    <named-config name="mysql">  
        <property name="driverClass">com.mysql.jdbc.Driver</property>  
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/CoupleSpace</property>  
        <property name="user">root</property>  
        <property name="password">root</property>  
        <property name="initialPoolSize">10</property>  
        <property name="maxIdleTime">30</property>  
        <property name="maxPoolSize">100</property>  
        <property name="minPoolSize">10</property>  
        <property name="maxStatements">200</property>  
    </named-config>  
    
    <!--配置连接池2-->
    ......
    <!--配置连接池3-->
    ......
    <!--配置连接池4-->
    ......
</c3p0-config>
```

3：创建c3p0utils类，定义获取connection，释放connection的方法，但是，这里推荐使用DButils

那DButils又是什么呐？

## 三：DBUtils简介：

1：DBUtils是什么？  

 Dbutils：主要是封装了JDBC的代码，简化dao层的操作。在使用DButils之前，我们Dao层（这个应该不用说了吧，就是相当于直接对数据库进行的操作的层）使用的技术是JDBC，但是jdbc弊端很多，所以就引入了DBUtils这一插件

2：DBUtils有什么好处？

对于数据表的读操作，他可以把结果转换成List，Array，Set等java集合，便于程序员操作；

对于数据表的写操作，也变得很简单（只需写sql语句

可以使用数据源，使用数据库连接池等技术来优化性能--重用已经构建好的数据库连接对象

3：怎么用DBUtils呐？

<1>导入jar包

<2>配合C3P0使用，配置C3P0配置文件

<3>创建JDBCUtils工具类（一般放在com.xqc.XXX.utils包下）

```java
  /**
   * JDBC工具类:
   *  加载驱动
   *   获得连接
   *   释放资源
   *   代码都重复.
   *
   */
  public class JDBCUtils {
     
    private static ComboPooledDataSource dataSource = new ComboPooledDataSource();
     
     // 获得连接池:
     public static DataSource getDataSource(){
         return dataSource;
     }
     
     // 获得连接
     public static Connection getConnection() {
         Connection conn = null;
         try {
            conn = dataSource.getConnection();
         } catch (SQLException e) {
             e.printStackTrace();
         }
         return conn;
     }
 }
```



<4>以登陆功能为例演示，在Servlet中把锅甩给service，service甩锅给Dao层的实现类，执行对数据库操作

```java
 public class UserDaoImple implements UserDao {
 
     public User login(User user) {
 //获取QueryRunner对象进行操作
          QueryRunner queryRunner = new QueryRunner(JDBCUtils.getDataSource());
  //sql语句
          String sql = "select * from user where username = ? and password = ?";
          User existUser;
          try {
 //直接使用query方法执行sql语句，并用beanHandle将返回结果的第一行封装为User对象
             existUser = queryRunner.query(sql, new BeanHandler<User>(User.class), user.getUsername(),user.getPassword());
         } catch (SQLException e) {
             e.printStackTrace();
             throw new RuntimeException("用户登录失败!");
         }
         return existUser;
     }
 
 }
```

至此，结束，但是在写登陆时我还用到了BeanUtils，在这我也顺道记录一下吧！

## 四：BeanUtils简介：

1：BeanUtils又是什么呐？

BeanUtils工具是一种方便我们对JavaBean进行操作的工具，是Apache组织下的产品

2：为什么要使用BeanUtils呐！他有什么好处吗？

1）beanUtils 可以便于对javaBean的属性进行赋值。

2）beanUtils 可以便于对javaBean的对象进行赋值。

3）beanUtils可以将一个MAP集合的数据拷贝到一个javabean对象中。



3：怎么用呐！我们平时将一些数据赋给Bean时，要考虑Bean中的数据类型，但是如果用BeanUtils的话就不用考虑折磨多了！比如：

```java
 // 接收数据
 Map<String, String[]> map = req.getParameterMap();
 User user = new User();
 // 封装数据
 BeanUtils.populate(user, map);
```

上边我们就直接把接受到的Map直接封装进了User对象，操作简单

同样拷贝时也是一样

```java
        //1.生成对象
       Map<String,Object> map = new HashMap<String,Object>();
     
          //2.给一些参数
          map.put("id", 2);
          map.put("name", "EZ");
          map.put("age", 22);
          map.put("classID", 3);
          map.put("birthday", new Date());
        
         //需求：把map的属性值拷贝到S中
        Student s = new Student();
        BeanUtils.copyProperties(s, map);
```



 