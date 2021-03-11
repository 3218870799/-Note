# 第 1 章 框架概述

## 1.3 JDBC 编程

### 1.3.1 ：JDBC 编程

```java
public void findStudent() {
    Connection conn = null;
    Statement stmt = null;
    ResultSet rs = null;
    try {
        //注册mysql驱动
        Class.forName("com.mysql.jdbc.Driver");
        //连接数据的基本信息 url ，username，password
        String url = "jdbc:mysql://localhost:3306/springdb";
        String username = "root";
        String password = "123456";
        //创建连接对象
        conn = DriverManager.getConnection(url, username, password);
        //保存查询结果
        List<Student> stuList = new ArrayList<>();
        //创建Statement, 用来执行sql语句
        stmt = conn.createStatement();
        //执行查询，创建记录集，
        rs = stmt.executeQuery("select * from student");
        while (rs.next()) {
            Student stu = new Student();
            stu.setId(rs.getInt("id"));
            stu.setName(rs.getString("name"));
            stu.setAge(rs.getInt("age"));
            //从数据库取出数据转为Student对象，封装到List集合
            stuList.add(stu);
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        try {
            //关闭资源
            if (rs != null) ;
            {
                rs.close();
            }
            if (stmt != null) {
                stmt.close();
            }
            if (conn != null) {
                conn.close();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 1.3.2：缺陷

1.  代码比较多，开发效率低

2.  需要关注 Connection ,Statement, ResultSet 对象创建和销毁

3.  对 ResultSet 查询的结果，需要自己封装为 List

4.  重复的代码比较多些

5.  业务代码和数据库的操作混在一起

## 1.4 MyBatis 框架概述

MyBatis 框架：

MyBatis 本是 apache 的一个开源项目 iBatis, 2010 年这个项目由 apache softwarefoundation 迁移到了 google code，并且改名为 MyBatis 。2013 年 11 月迁移到 Github。

iBATIS 一词来源于“internet”和“abatis”的组合，是一个基于 Java 的持久层框架。iBATIS 提供的持久层框架包括 SQL Maps 和 Data AccessObjects（DAOs）当前，最新版本是 MyBatis 3.5.1 ，其发布时间是 2019 年 4 月 8 日。

### 1.4.1Mybatis 解决的主要问题

减轻使用 JDBC 的复杂性，不用编写重复的创建 Connetion , Statement ;不用编写关闭资源代码。直接使用 java 对象，表示结果数据。让开发者专注 SQL 的处理。 其他分心的工作由 MyBatis 代劳。

MyBatis 可以完成：

1.  注册数据库的驱动，例如 Class.forName(“com.mysql.jdbc.Driver”))
2.  创建 JDBC 中必须使用的 Connection ， Statement， ResultSet 对象
3.  从 xml 中获取 sql，并执行 sql 语句，把 ResultSet 结果转换 java 对象

```java
List<Student> list = new ArrayLsit<>();
ResultSet rs = state.executeQuery(“select * from student”);
while(rs.next){
    Student student = new Student();
    student.setName(rs.getString(“name”));
    student.setAge(rs.getInt(“age”));
    list.add(student);
}
```

4.关闭资源

ResultSet.close() , Statement.close() , Conenection.close()

缺点：

1、SQL 语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写 SQL 语句的功底有一定要求。

2、SQL 语句依赖于数据库，导致数据库移植性差，不能随意更换数据库

### 对 Hibernate 对比

Mybatis 是一个半自动的 ORM 框架，在查询关联对象或关联集合对象时，需要手动编写 sql 语句来完成;

Hibernate 是全自动 ORM 映射工具，查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，不需要编写 sql.

Mybatis 直接编写原生态 sql，可以严格控制 sql 执行性能，灵活度高， 非常适合对性能要求高，需求变化频繁的项目；但是如果涉及到较多的字段或者关联多表时，sql 语句编写量大且对开发人的 sql 语句编写功底要求高。

Hibernate 对象/关系映射能力强，数据库无关性好，适合需求变化不大的项目，使用 hibernate 开发可以节省很多代码，提高效率。

# 第 2 章 MyBatis 框架快速入门

## **2.1** 入门案例

MyBatis 开发准备

搭建 MyBatis 开发环境，实现第一个案例

下载 mybatis

https://github.com/mybatis/mybatis-3/releases

（1） 创建 mysql 数据库和表

![](media/f6e25e9cbaee11363e6a61d1f3b79057.jpg)

CREATE TABLE \`student\` (

\`id\` int(11) NOT NULL ,

\`name\` varchar(255) DEFAULT NULL,

\`email\` varchar(255) DEFAULT NULL,

\`age\` int(11) DEFAULT NULL,

PRIMARY KEY (\`id\`)

) ENGINE=InnoDB DEFAULT CHARSET=utf8;

**（**2**） 创建** maven **工程**

创建 maven 工程，信息如下：模板：

![](media/5918b8e48d940864208054665fda38ba.jpg)

![](media/047c7e98264f3dc155daac9911506f59.jpg)

（**3**） 删除默认创建的 **App** 类文件

![](media/3e44de9861a7fa5ddf061739558314a2.jpg)

（4）添加 Maven 依赖

```xml
<dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
</dependency>
<dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.1</version>
</dependency>
<dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
    <version>5.1.9</version>
    </dependency>
</dependencies>

```

**（**5**） 加入** maven **插件**

```xml
 <build>
    <resources>
      <resource>
        <directory>src/main/java</directory><!--所在的目录-->
        <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
   </resources>
    <plugins>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
        </configuration>
      </plugin>
    </plugins>
</build>

```

**（**6**） 编写** Student **实体类**

创建包 com.xqc.domain, 包中创建 Student 类

```java
package com.xqc.domain;
/**
*	<p>Description: 实体类 </p>
*	<p>Company: http://www.xqc.com
 */
 public class Student {
    //属性名和列名一样
     private Integer id;
     private String name;
     private String email;
     private Integer age;
    // set ,get , toString
}
```

**（**7**） 编写** Dao **接口** StudentDao

创建 com.xqc.dao 包，创建 StudentDao 接口

```java
package com.xqc.dao;
import com.xqc.domain.Student;
import java.util.List;
/**
 * <p>Description: Dao接口 </p>
 * <p>Company: http://www.xqc.com
 */
public interface StudentDao {
    /*查询所有数据*/
	List<Student> selectStudents();
}

```

**（**8**） 编写** Dao **接口** Mapper **映射文件** StudentDao.xml

要求：

1.  在 dao 包中创建文件 StudentDao.xml
2.  要 StudentDao.xml 文件名称和接口 StudentDao 一样，区分大小写的一样。

```xml
 <?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
		"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--
     namespace：必须有值，自定义的唯一字符串
		推荐使用：dao接口的全限定名称
-->
<mapper namespace="com.xqc.dao.StudentDao">
    <!--
       <select>: 查询数据， 标签中必须是select语句
			id:  sql语句的自定义名称，推荐使用dao接口中方法名称，
			使用名称表示要执行的sql语句
			resultType: 查询语句的返回结果数据类型，使用全限定类名
    -->
    <select id="selectStudents" resultType="com.xqc.domain.Student">
        <!--要执行的sql语句-->
        select id,name,email,age from student
    </select>
</mapper>

```

（**9**） 创建 **MyBatis** 主配置文件

项目 src/main 下创建 resources 目录，设置 resources 目录为 resources root
创建主配置文件：名称为 mybatis.xml

说明：主配置文件名称是自定义的，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"         "http://mybatis.org/dtd/mybatis-3-config.dtd">

    <!--配置mybatis环境-->
    <environments default="mysql">
        <!--id:数据源的名称-->
        <environment id="mysql">
            <!--配置事务类型：使用JDBC事务（使用Connection的提交和回滚）-->
            <transactionManager type="JDBC"/>
            <!--数据源dataSource：创建数据库Connection对象
				type: POOLED 使用数据库的连接池
            -->
            <dataSource type="POOLED">
                <!--连接数据库的四个要素-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--告诉mybatis要执行的sql语句的位置-->
        <mapper resource="com/xqc/dao/StudentDao.xml"/>
</mappers>
<configuration>

```

支持中文的 url

**jdbc:mysql://localhost:3306/ssm?useUnicode=true&characterEncoding=utf-8**

**（**10**） 创建测试类** MyBatisTest

src/test/java/com/xqc/ 创建 MyBatisTest.java 文件

```java
@Test
public void testStart() throws IOException{
    //1.mybatis主配置文件
    String config = "mybatis-config.xml";
    //2:读取配置文件
    InputStream in = Resources.getResourceAsStream(config);
    //3.创建SqlSessionFactory对象,目的是获取SqlSession
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
    //4.获取SqlSession,SqlSession能执行sql语句
    SqlSession session = factory.openSession();
    //5.执行SqlSession的selectList()
    List<Student> studentList =      session.selectList("com.xqc.dao.StudentDao.selectStudents");
    //6.循环输出查询结果
    studentList.forEach( student -> System.out.println(student));
    //7.关闭SqlSession，释放资源     session.close();

}

```

List\<Student\> studentList =
session.selectList("com.xqc.dao.StudentDao.selectStudents");
近似等价的 jdbc 代码

Connection conn = 获取连接对象

String sql=” select id,name,email,age from student”

PreparedStatement ps = conn.prepareStatement(sql);

ResultSet rs = ps.executeQuery();

（**11**） 配置日志功能

mybatis.xml 文件加入日志配置，可以在控制台输出执行的 sql 语句和参数

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING" />
</settings>
```

## 2.2 **基本的** CURD

查询一个 selectOne

insert ,update ,delete

### **2.2.1** **insert**

（1） StudentDao 接口中增加方法

```java
**int** insert Student(Student student);
```

**（**2**）** StudentDao.xml **加入** sql **语句**

\<**insert id="insertStudent"**\> insert into student(id,name,email,age)
values(\#{id},\#{name},\#{email},\#{age})

\</**insert**\>

（**3**） 增加测试方法

\@Test

**public void** testInsert() **throws** IOException {

**//1.mybatis**主配置文件

String config = **"mybatis-config.xml"**;

**//2.**读取配置文件

InputStream in = Resources._getResourceAsStream_(config);

**//3.**创建**SqlSessionFactory**对象

SqlSessionFactory factory = **new** SqlSessionFactoryBuilder().build(in);

**//4.**获取**SqlSession**

SqlSession session = factory.openSession();

**//5.**创建保存数据的对象

Student student = **new** Student();

student.setId(1005);

student.setName(**"**张丽**"**); student.setEmail(**"zhangli\@163.com"**);
student.setAge(20); **//6.**执行插入**insert int** rows = session.insert(

System.**out**.println(**"**增加记录的行数**:"**+rows);

**//8.**关闭**SqlSession** session.close();

}

### 2.2.2 update

（1） StudentDao 接口中增加方法

int updateStudent(Student student);

（2） StudentDao.xml 增加 sql 语句

\<update id="updateStudent"\> update student set age = \#{age} where id=\#{id}

\</update\>

（3） 增加测试方法

\@Test public void testUpdate() throws IOException {

*//1.mybatis*主配置文件

String config = "mybatis-config.xml";

*//2.*读取配置文件

InputStream in = Resources._getResourceAsStream_(config);

*//3.*创建*SqlSessionFactory*对象

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);

*//4.*获取*SqlSession*

SqlSession session = factory.openSession();

*//5.*创建保存数据的对象

Student student = new Student(); student.setId(1005);*//*要修改的*id*
student.setAge(30); *//*要修改的年龄值

*//6.*执行更新*update* int rows = session.update(

"com.xqc.dao.StudentDao.updateStudent",student);

*//7.*提交事务

session.commit();

System._out_.println("修改记录的行数:"+rows);

*//8.*关闭*SqlSession* session.close();

}

(4)问

Mybatis 如果 Update 一个没有的数据项会怎么办？？

实验证明：不会怎么样，也不会报错，也不会增加数据

### 2.2.3 delete

（1） StudentDao 接口中增加方法

int deleteStudent(int id);

（2） StudentDao.xml 增加 sql 语句

\<delete id="deleteStudent"\> delete from student where id=\#{studentId}

\</delete\>

（3） 增加测试方法

\@Test public void testDelete() throws IOException {

*//1.mybatis*主配置文件

String config = "mybatis-config.xml";

*//2.*读取配置文件

InputStream in = Resources._getResourceAsStream_(config);

*//3.*创建*SqlSessionFactory*对象

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);

*//4.*获取*SqlSession*

SqlSession session = factory.openSession();

*//5.*删除的*id* int id = 1001;

*//6.*执行删除*delete* int rows = session.delete(

"com.xqc.dao.StudentDao.deleteStudent",id);

*//7.*提交事务

session.commit();

System._out_.println("修改记录的行数:"+rows);

*//8.*关闭*SqlSession* session.close();

}

## 2.3 MyBatis **对象分析**

### 2.3.1 对象使用

SqlSession , SqlSessionFactory 等

（1） Resources 类

Resources 类，顾名思义就是资源，用于读取资源文件。其有很多方法通过加载并解析资源文件，返回不同类型的 IO 流对象。

**（**2**）** SqlSessionFactoryBuilder **类**

SqlSessionFactory 的创建，需要使用 SqlSessionFactoryBuilder 对象的 build()方法。由于 SqlSessionFactoryBuilder 对象在创建完工厂对象后，就完成了其历史使命，即可被销毁。所以，一般会将该 SqlSessionFactoryBuilder 对象创建为一个方法内的局部对象，方法结束，对象销毁。

**（**3**）** SqlSessionFactory **接口**

SqlSessionFactory 接口对象是一个重量级对象（系统开销大的对象），是线程安全的，所以一个应用只需要一个该对象即可。创建 SqlSession 需要使用 SqlSessionFactory 接口的 openSession()方法。

SqlSessionFactory 是 MyBatis 的关键对象,它是个单个数据库映射关系经过编译后的内存镜像.SqlSessionFactory 对象的实例可以通过 SqlSessionFactoryBuilder 对象类获得,SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先定制的 Configuration 的实例构建出 SqlSessionFactory 的实例.每一个 MyBatis 的应用程序都以一个 SqlSessionFactory 对象的实例为核心.同时 SqlSessionFactory 也是线程安全的,SqlSessionFactory 一旦被创建,应该在应用执行期间都存在.在应用运行期间不要重复创建多次,建议使用单例模式.SqlSessionFactory 是创建 SqlSession 的工厂.

openSession(true)：创建一个有自动提交功能的 SqlSession

openSession(false)：创建一个非自动提交功能的 SqlSession，需手动提交

openSession()：同 openSession(false)

mybatis 框架主要是围绕着 SqlSessionFactory 进行的，创建过程大概如下：

```xml
(1)、定义一个Configuration对象，其中包含数据源、事务、mapper文件资源以及影响数据库行为属性设置settings

(2)、通过配置对象，则可以创建一个SqlSessionFactoryBuilder对象

(3)、通过 SqlSessionFactoryBuilder 获得SqlSessionFactory 的实例。

(4)、SqlSessionFactory 的实例可以获得操作数据的SqlSession实例，通过这个实例对数据库进行操
```

**（**4**）** SqlSession **接口**

SqlSession 接口对象用于执行持久化操作。一个 SqlSession 对应着一次数据库会话，一次会话以 SqlSession 对象的创建开始，以 SqlSession 对象的关闭结束。

SqlSession 的实例不能被共享，SqlSession 接口对象是线程不安全的，所以每次数据库会话结束前，需要马上调用其 close()方法，将其关闭。再次需要会话，再次创建。 SqlSession 在方法内部创建，.使用完 SqlSeesion 之后关闭 Session 很重要,应该确保使用 finally 块来关闭它.

### 2.3.2 创建工具类

**（**1**） 创建** MyBatisUtil **类**

```java

```

**（**2**） 使用** MyBatisUtil 类

## 2.4 MyBatis 使用传统 Dao 开发方式

使用 Dao 的实现类,操作数据库

### 2.4.1 Dao 开发

（**1**） 创建 **Dao** 接口实现类

```java
public class StudentDaoImpl implemnets StudentDao{

}
```

（**2**） 实现接口中 **select** 方法

```java
public List<Student> selectStudent(){
    SqlSession session = MybatisUtil.getSqlSession();
    List<Student> studentList = session.selectList("com.node.dao.StudentDao.selectStudents");
    session.close();
    return studentList;
}
```

测试查询操作:

MyBatisTest 类中创建 StudentDaoImpl 对

**public class** MyBatisTest {

StudentDao **studentDao** = **new** StudentDaoImpl();

}

\@Test **public void** testSelect() **throws** IOException { **final**
List\<Student\> studentList = **studentDao**.selectStudents();
studentList.forEach( stu -\> System.**out**.println(stu)); }

（**3**） 实现接口中 **insert** 方法

**public int** insertStudent(Student student) { SqlSession session =
MyBatisUtil._getSqlSession_(); **int** nums = session.insert(

**"com.xqc.dao.StudentDao.insertStudent"**,student); session.commit();
session.close(); **return** nums;

}

测试 insert \@Test

**public void** testInsert() **throws** IOException { Student student = **new**
Student(); student.setId(1006); student.setName(**"**林浩**"**);
student.setEmail(**"linhao\@163.com"**); student.setAge(26);

**int** nums = **studentDao**.insertStudent(student);

System.**out**.println(**"**使用**Dao**添加数据**:"**+nums);

}

（**4**） 实现接口中 **update** 方法

**public int** updateStudent(Student student) { SqlSession session =
MyBatisUtil._getSqlSession_(); **int** nums = session.insert(

**"com.xqc.dao.StudentDao.updateStudent"**,student); session.commit();
session.close(); **return** nums;

}

测试 update \@Test

**public void** testUpdate() **throws** IOException { Student student = **new**
Student(); student.setId(1006); student.setAge(28);

**int** nums = **studentDao**.updateStudent(student);

System.**out**.println(**"**使用**Dao**修改数据**:"**+nums);

}

（**5**） 实现接口中 **delete** 方法

**public int** deleteStudent(**int** id) {

SqlSession session = MyBatisUtil._getSqlSession_(); **int** nums =
session.insert(

**"com.xqc.dao.StudentDao.deleteStudent"**,1006); session.commit();
session.close(); **return** nums;

}

测试 delete \@Test

**public void** testDelete() **throws** IOException { **int** nums =
**studentDao**.deleteStudent(1006);
System.**out**.println(**"**使用**Dao**修改数据**:"**+nums);

}

### 2.4.2 传统 Dao 开发方式的分析

在前面例子中自定义 Dao 接口实现类时发现一个问题：Dao 的实现类其实并没有干什么实质性的工作，它仅仅就是通过 SqlSession 的相关 API 定位到映射文件 mapper 中相应 id 的 SQL 语句，真正对 DB 进行操作的工作其实是由框架通过 mapper 中的 SQL 完成的。

所以，MyBatis 框架就抛开了 Dao 的实现类，直接定位到映射文件 mapper 中的相应 SQL 语句，对 DB 进行操作。这种对 Dao 的实现方式称为 Mapper 的动态代理方式。

Mapper 动态代理方式无需程序员实现 Dao 接口。接口是由 MyBatis 结合映射文件自动生成的动态代理实现的。

# 第 3 章 Dao 代理

## 3.1 Dao 代理实现 CURD

### 3.1.1：步骤

（**1**） 去掉 **Dao** 接口实现类

![](media/4c01e1b456634756d109182f34eb4bd3.jpg)

只需调用 SqlSession 的 getMapper()方法，即可获取指定接口的实现类对象。该方法的参数为指定 Dao 接口类的 class 值。

```java
SqlSession session = *factory*.openSession();

StudentDao dao = session.getMapper(StudentDao.**class**);
```

使用工具类:

```java
StudentDao **studentDao** = MyBatisUtil.*getSqlSession*().getMapper(StudentDao.**class**);
```

（**3**） 使用 **Dao** 代理对象方法执行 **sql** 语句

select 方法:

```java
@Test
public void testSelect() throws IOException{
    final List<Student> studentList = studentDao.selectStudents();
    studentList.foreach(stu->System.out.println(stu));
}
```

update 方法

```java
@Test
public void testUpdate() throws IOException{
    Student student = new Student();
    student.setId(10086);
    student.setAge(28);
    int nums = studentDao.updateStudent(student);
    System.out.println(nums);
}
```

delete 方法

```java
StudentDao.delectStudent(1006);
```

### 3.1.2 原理

动态代理

![](media/bdd7936aae7acaded41ed39c477e1ea5.jpg)

MapperProxy 类定义:

![](media/1bbe485468e512390989caac563413a9.jpg)

![](media/248dec87a7d2fda42a63339a5d7aab61.jpg)

![](media/46b55961b4f879a351f63b630bd85a43.jpg)

## 3.2 **深入理解参数**

### 3.2.1 parameterType

parameterType: 接口中方法参数的类型，类型的完全限定名或别名。这个属性是可选的，因为 MyBatis 可以推断出具体传入语句的参数，默认值为未设置（unset）。

接口中方法的参数从 java 代码传入到 mapper 文件的 sql 语句。

\<select\>,\<insert\>,\<update\>,\<delete\>都可以使用 parameterType 指定类型。

例如：

```xml
<delect id="delectById" parameterType="int"></delect>
<delect id="delectById" parameterType="java.lang.Integer"></delect>
```

### 3.2.2 MyBatis 传递参数

从 java 代码中把参数传递到 mapper.xml 文件。

#### 一个简单参数

Dao 接口中方法的参数只有一个简单类型（java 基本类型和 String），占位符 **\#{**任意字符 **}**，和方法的参数名无关。

mapper 文件：

```xml
<select id="selectById" resultType="com.xqc.domain.Student">
	select * from student where id = #{studentId}
</select>
```

\#{studentId} , studentId 是自定义的变量名称，和方法参数名无关。

#### 多个参数-使用@Param

当 Dao 接口方法多个参数，需要通过名称使用参数。在方法形参前面加入@Param(“自定义参数名”)，

mapper 文件使用\#{自定义参数名}。

例如

```java
List<Student> selectMultiParam(@Param("Name")String name,@Param("Age")int age);
```

mapper 文件：

```xml
<select id="selectMultiParam" resultType="com.xqc.domain.Student">
	select id,name.email,age from student where name= #{Name} or age= #{Age}
</select>
```

测试方法：

#### 多个参数-使用对象

使用 java 对象传递参数， java 的属性值就是 sql 需要的参数值。每一个属性就是一个参数。

语法格式： \#{ property,javaType=java 中数据类型名,jdbcType=数据类型名称 }

javaType, jdbcType 的类型 MyBatis 可以检测出来，一般不需要设置。

常用格式 \#{property }

![](media/195f0d28cc0995737980dea48aa54307.jpg)

创建保存参数值的对象 QueryParam

```java
package com.xqc.vo;
public class QueryParam{
    private String queryName;
    private int queryAge;
    //get和set
}
```

接口方法：

```java
List<Student> selectMultiObject(QueryParam queryParam);
```

mapper 文件：

```xml
<select id="selectMultiObject" resutlType="com.xqc.domain.Student">
	select id,name,email,age from student where name = #{queryName} or age = #{queryAge}
</select>
```

或者

```xml
<select id="selectMultiObject" resutlType="com.xqc.domain.Student">
	select id,name,email,age from student where name =#{queryName,javaType=string,jdbcType=VARCHAR}
    or age = #{queryAge,javaType=int,jdbcType=INTEGER}
</select>
```

？？？？？下面这种可以吗？

使用

\<**select id="selectMultiObject"
resultType="com.xqc.domain.Student"**\>

select id,name,email,age from student where name=\#{ queryParam .queryName} or
age =\#{ queryParam .queryAge}

\</**select**\>

#### 多个参数-按位置

参数位置从 0 开始， 引用参数语法 **\#{ arg** 位置 **}** ， 第一个参数是\#{arg0}，第二个是\#{arg1}

注意：mybatis-3.3 版本和之前的版本使用\#{0},\#{1}方式， 从 mybatis3.4 开始使用\#{arg0}方式。

接口方法：

```java
List<Student> selectByNameAndAge(String name,**int** age);
```

mapper 文件

```xml
<select id="selectByNameAndAge" resutlType="com.xqc.domain.Student">
	select * from student where name = #{arg0} or age = #{arg1}
</select>
```

#### 多个参数-使用 Map

Map 集合可以存储多个值，使用 Map 向 mapper 文件一次传入多个参数。Map 集合使用 String 的 key， Object 类型的值存储参数。 mapper 文件使用 \# { key } 引用参数值。

> 例如：

> Map\<String,Object\> data = new HashMap\<String,Object\>();

> data.put(“myname”,”李力”);

> data.put(“myage”,20);

接口方法：

List\<Student\> selectMultiMap(Map\<String,Object\> map)

mapper 文件：

\<**select id="selectMultiMap" resultType="com.xqc.domain.Student"**\>

select id,name,email,age from student where name=\#{myname} or age =\#{myage}

\</**select**\>

测试方法：

\@Test

**public void** testSelectMultiMap(){

Map\<String,Object\> data = **new** HashMap\<\>();
data.put(**"myname"**,**"**李力**"**);**// \#{myname}**
data.put(**"myage"**,20); **// \#{myage}**

List\<Student\> stuList = **studentDao**.selectMultiMap(data);

stuList.forEach( stu -\> System.**out**.println(stu));

}

### 3.2.8 \#和\$

\#将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。

如：where username = #{username} 会解析成

where username = "111"

$将传入的数据直接显示生成在 sql 中。

如：where username=${username}会解析成

where username = 111

#方式可以很大程度防止 sql 注入

$这样的参数会直接参与 sql 编译,从而不能避免注入攻击

**\#**：占位符，告诉 mybatis 使用实际的参数值代替。并使用 PrepareStatement
对象执行 sql 语句,

\#{…}代替 sql 语句的“?”。这样做更安全，更迅速，通常也是首选做法，

\<**select id="selectById" resultType="com.xqc.domain.Student"**\>

select id,name,email,age from student where id=\#{studentId}

\</**select**\>

转为 MyBatis 的执行是：

String sql=” select id,nasme,email,age from student where id=?”;

PreparedStatement ps = conn.prepareStatement(sql);

ps.setInt(1,1005);

解释：

where id=? 就是 where id=\#{studentId} ps.setInt(1,1005) , 1005 会替换掉
\#{studentId}

**\$** 字符串替换，告诉 mybatis 使用\$包含的“字符串”替换所在位置。使用 Statement
把 sql 语句和\${}的内容连接起来。主要用在替换表名，列名，不同列排序等操作。

例 1： 分别使用 id ， email 列查询 Student 接口方法：

mapper 文件：

\<**select id="findById" resultType="com.xqc.domain.Student"**\>

select \*\*\* from student where id=\#{studentId}

\</**select**\>

**\<**select id="findByEmail" resultType="com.xqc.domain.Student"**\>**

**select \* from student where email=\#{stuentEmail}**

\</**select**\>

测试方法：

\@Test

**public void** testFindStuent(){

Student student1 = **studentDao**.findById(1002);

System.**out**.println(**"findById:"**+student1);

Student student2 = **studentDao**.findByEmail(**"zhou\@126.net"**);

System.**out**.println(**"findByEmail:"**+student2);

}

例 2：通用方法，使用不同列作为查询条件

mapper 文件：

**\<**select id="findByDiffField"
resultType="com.xqc.domain.Student"**\>**

**select \* from student where** \${col} **=** \#{cval}

\</**select**\>

测试方法：

\@Test

**public void** testFindDiffField(){

Student student1 = **studentDao**.findByDiffField(**"id"**,1002);

System.**out**.println(**"**按**id**列查询**:"**+student1);

Student student2 =
**studentDao**.findByDiffField(**"email"**,**"zhou\@126.net"**);

System.**out**.println(**"**按**email**列查询**:"**+student2);

}

## 3.3 封装 MyBatis 输出结果

### 3.3.1：resultType

resultType: 执行 sql 得到 ResultSet 转换的类型，使用类型的完全限定名或别名。
注意如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身。resultType 和
resultMap，不能同时使用。

![](media/aad42d3b558848ba30b5ce10295be87d.jpg)

简单类型

接口方法：

**int** countStudent();

mapper 文件：

\<**select id="countStudent" resultType="int"**\>

select _count_(\*\*\*) from student

\</**select**\>

\@Test

**public void** testRetunInt(){

**int** count = **studentDao**.countStudent();

System.**out**.println(**"**学生总人数：**"**+ count);

对象类型

接口方法：

Student selectById(**int** id);

mapper 文件：

\<**select id="selectById" resultType="com.xqc.domain.Student"**\>

select id,name,email,age from student where id=\#{studentId}

\</**select**\>

框架的处理： 使用构造方法创建对象。调用 setXXX 给属性赋值。

Student student = new Student();

| sql 语句列 | java 对象方法                     |                                                                 |
| ---------- | --------------------------------- | --------------------------------------------------------------- |
| id         | setId( rs.getInt(“id”) )          | 调用列名对应的 set 方法 id 列 --- setId() name 列 --- setName() |
| name       | setName( rs.getString(“name”) )   |                                                                 |
| email      | setEmail( rs.getString(“email”) ) |                                                                 |
| age        | setAge( rs.getInt(“age”) )        |                                                                 |

注意：Dao 接口方法返回是集合类型，需要指定集合中的类型，不是集合本身。

![](media/9084215025d3d002abdd1e5a7107f628.jpg)

**Map**

sql 的查询结果作为 Map 的 key 和 value。推荐使用 Map\<Object,Object\>。

注意：Map 作为接口返回值，sql
语句的查询结果最多只能有一条记录。大于一条记录是错误。

接口方法：

Map\<Object,Object\> selectReturnMap(int id);

mapper 文件：

\<**select id="selectReturnMap" resultType="java.util.HashMap"**\>

select name,email from student where id = \#{studentId}

\</select\>

测试方法：

\@Test

**public void** testReturnMap(){

Map\<Object,Object\> retMap = **studentDao**.selectReturnMap(1002);

System.**out**.println(**"**查询结果是**Map:"**+retMap);

}

### 3.3.2 resultMap

resultMap 可以自定义 sql 的结果和 java
对象属性的映射关系。更灵活的把列值赋值给指定属性。常用在列名和 java
对象属性名不一样的情况。

使用方式：

1.先定义 resultMap,指定列名和属性的对应关系。

2.在\<select\>中把 resultType 替换为 resultMap。

接口方法：

List\<Student\> selectUseResultMap(QueryParam param);

**id:**自定义的唯一名称，在**\<select\>**使用
**type:**期望转为的**java**对象的全限定名称或别名

**--\>**

**\<**resultMap id="studentMap" type="com.xqc.domain.Student"**\>**

**\<!--** 主键字段使用**id --\>**

\<**id column="id" property="id"** /\>

**\<!--**非主键字段使用**result--\>**

**\<**result column="name" property="name"**/\> \<**result column="email"
property="email" **/\>**

\<**result column="age" property="age"** /\>

\</**resultMap**\>

**\<!--resultMap: resultMap**标签中的**id**属性值**--\>**

\<**select id="selectUseResultMap" resultMap="studentMap"**\> select
id,name,email,age from student where name=\#{queryName} or age=\#{queryAge}

\</**select**\>

测试方法：

\@Test

**public void** testSelectUseResultMap(){ QueryParam param = **new**
QueryParam(); param.setQueryName(**"**李力**"**); param.setQueryAge(20);

List\<Student\> stuList = **studentDao**.selectUseResultMap(param);
stuList.forEach( stu -\> System.**out**.println(stu));

}

### 3.3.3 实体类属性名和列名不同的处理方式

（1） 使用列别名和\<resultType\>

步骤：

1. 创建新的实体类 PrimaryStudent package com.xqc.domain;

/\*\*

\<p\>Description: 实体类 \</p\>

\<p\>Company: http://www.xqc.com

\*/

List\<PrimaryStudent\> selectUseFieldAlias(QueryParam param);

3. mapper 文件：

\<select id="selectUseFieldAlias"
resultType="com.xqc.domain.PrimaryStudent"\>

select id as stuId, name as stuName,age as stuAge from student where
name=\#{queryName} or age=\#{queryAge} \</select\>

4.测试方法

\@Test

public void testSelectUseFieldAlias(){

> QueryParam param = new QueryParam();

> param.setQueryName("李力");

> param.setQueryAge(20);

> List\<PrimaryStudent\> stuList;

> stuList = studentDao.selectUseFieldAlias(param);

> stuList.forEach( stu -\> System._out_.println(stu));

}

（2） 使用\<resultMap\>

步骤：

接口方法

List\<PrimaryStudent\> selectUseDiffResultMap(QueryParam param);

mapper 文件：

_\<!--_ 创建*resultMap id:*自定义的唯一名称，在*\<select\>*使用
*type:*期望转为的*java*对象的全限定名称或别名

_--\>_

\<resultMap id="primaryStudentMap"

type="com.xqc.domain.PrimaryStudent"\>

_\<!--_ 主键字段使用*id --\>*

\<id column="id" property="stuId" /\>

*\<!--*非主键字段使用*result--\>*

\<result column="name" property="stuName"/\> \<result column="age"
property="stuAge" /\>

\</resultMap\>

*\<!--resultMap: resultMap*标签中的*id*属性值*--\>*

\<select id="selectUseDiffResultMap" resultMap="primaryStudentMap"\> select
id,name,email,age from student where name=\#{queryName} or age=\#{queryAge}

\</select\>

3. 测试方法

\@Test

public void testSelectUseDiffResultMap(){

QueryParam param = new QueryParam();

param.setQueryName("李力");

param.setQueryAge(20);

List\<PrimaryStudent\> stuList;

stuList = studentDao.selectUseDiffResultMap(param); stuList.forEach( stu -\>
System._out_.println(stu));

}

## 3.4 **模糊** like

模糊查询的实现有两种方式， 一是 java 代码中给查询数据加上“%” ; 二是在 mapper
文件 sql 语句的条件位置加上“%”

需求：查询姓名有“力”的

例 **1: java** 代码中提供要查询的 “**%**力**%**” 接口方法：

List\<Student\> selectLikeFirst(String name);

mapper 文件：

\<**select id="selectLikeFirst" resultType="com.xqc.domain.Student"**\>
select id,name,email,age from student where name like \#{studentName}

\</**select**\>

**public void** testSelectLikeOne(){

String name=**"%**力**%"**;

List\<Student\> stuList = **studentDao**.selectLikeFirst(name); stuList.forEach(
stu -\> System.**out**.println(stu));

}

例 2：mapper 文件中使用 like name "%" \#{xxx} "%"

接口方法：

List\<Student\> selectLikeSecond(String name);

mapper 文件：

\<**select id="selectLikeSecond" resultType="com.xqc.domain.Student"**\>
select id,name,email,age from student where name like "%" \#{studentName} "%"

\</**select**\>

**public void** testSelectLikeSecond(){

String name=**"**力**"**;

List\<Student\> stuList = **studentDao**.selectLikeSecond(name);
stuList.forEach( stu -\> System.**out**.println(stu));

}

# 第 4 章 MyBatis 框架动态 SQL

动态 SQL，通过 MyBatis 提供的各种标签对条件作出判断以实现动态拼接 SQL
语句。这里的条件判断使用的表达式为 OGNL 表达式。常用的动态 SQL
标签有\<if\>、\<where\>、\<choose/\>、\<foreach\>等。

MyBatis 的动态 SQL 语句，与 JSTL 中的语句非常相似。

动态
SQL，主要用于解决查询条件不确定的情况：在程序运行期间，根据用户提交的查询条件进行查询。提交的查询条件不同，执行的
SQL
语句不同。若将每种可能的情况均逐一列出，对所有条件进行排列组合，将会出现大量的
SQL 语句。此时，可使用动态 SQL 来解决这样的问题

## **4.1** 环境准备

> 创建新的 maven 项目，加入 mybatis ， mysql 驱动依赖

> 创建实体类 Student ， StudentDao 接口，StudentDao.xml , mybatis.xml ,
> 测试类使用之前的表 student。

在 mapper 的动态 SQL
中若出现大于号（\>）、小于号（\<）、大于等于号（\>=），小于等于号（\<=）等符号，最好将其转换为实体符号。否则，XML
可能会出现解析出错问题。

> 特别是对于小于号（\<），在 XML 中是绝不能出现的。否则解析 mapper
> 文件会出错。

> 实体符号表：

| \<  | 小于     | \&lt;  |
| --- | -------- | ------ |
| \>  | 大于     | \&gt;  |
| \>= | 大于等于 | \&gt;= |
| \<= | 小于等于 | \&lt;= |

## 4.2 \<if\>

对于该标签的执行，当 test 的值为 true 时，会将其包含的 SQL 片断拼接到其所在的 SQL 语句中。

语法：\<if test=”条件”\> sql 语句的部分 \</if\>

接口方法：

List\<Student\> selectStudentIf (Student student);

mapper 文件：

```xml
<select id="selectStudentIf" resultType="com.node.domain.Student">
	select id,name,email,age from student where 1=1
    <if test="name!=null and name!=''">
        and name=#{name}
    </if>
    <if test="age>0">
    	and age &gt;#{age}
    </if>
</select>
```

测试:

```java
List<Student> studentList = studentDao.selectStudentIf(param);

studentList.forEach( stu -\> System.**out**.println(stu));
```

Mybatis 映射几种：

1：没有传该数据项就不插入

2：如果没有传该对象就插入一个默认值

3：查询条件不为空就添加该查询条件

![](media/8bc55675eaf753a1ac11940957cefa7b.png)

## 4.3 \<where\>

\<if/\>标签的中存在一个比较麻烦的地方：需要在 where 后手工添加 1=1
的子句。因为，若 where 后的所有\<if/\>条件均为 false，而 where 后若又没有 1=1
子句，则 SQL 中就会只剩下一个空的 where，SQL 出错。所以，在 where
后，需要添加永为真子句
1=1，以防止这种情况的发生。但当数据量很大时，会严重影响查询效率。

使用\<where/\>标签，在有查询条件时，可以自动添加上 where
子句；没有查询条件时，不会添加 where 子句。需要注意的是，第一个\<if/\>标签中的
SQL 片断，可以不包含 and。不过，写上 and 也不错，系统会将多出的 and
去掉。但其它\<if/\>中 SQL 片断的 and，必须要求写上。否则 SQL 语句将拼接出错 。

语法：\<where\> 其他动态 sql \</where\>

接口方法：

List\<Student\> selectStudentWhere (Student student);

mapper 文件：

\<**select id="selectStudentWhere"
resultType="com.xqc.domain.Student"**\>

select id,name,email,age from student

\<**where**\>

\<if test="name != null and name !='' "\>

and name = \#{name}

\</**if**\>

\<**if test="age \> 0 "**\>

and age **\&gt;** \#{age}

\</**if**\>

\</**where**\>

\</**select**\>

## 4.4 \<foreach\>

\<foreach/\>标签用于实现对于数组与集合的遍历。对其使用，需要注意：

collection 表示要遍历的集合类型, list ，array 等。

open、close、separator 为对遍历内容的 SQL 拼接。

语法：

\<**foreach collection="**集合类型**" open="**开始的字符**"
close="**结束的字符**"** **item="**集合中的成员**"
separator="**集合成员之间的分隔符**"**\>

\#{item 的值}

\</**foreach**\>

（**1**） 遍历 **List\<**简单类型**\>**

表达式中的 List 使用 list 表示，其大小使用 list.size 表示。

需求：查询学生 id 是 1002,1005,1006

接口方法：

List\<Student\> selectStudentForList(List\<Integer\> idList);

mapper 文件：

\<**select id="selectStudentForList"
resultType="com.xqc.domain.Student"**\>

select id,name,email,age from student

\<**if test="list !=null and list.size \> 0 "**\>

where id in

\<**foreach collection="list" open="(" close=")"** **item="stuid"
separator=","**\>

\#{stuid}

\</**foreach**\>

\</**if**\>

\</**select**\>

**public void** testSelectForList() {

List\<Integer\> list = **new** ArrayList\<\>();

list.add(1002);

list.add(1005);

list.add(1006);

List\<Student\> studentList = **studentDao**.selectStudentForList(list);

studentList.forEach( stu -\> System.**out**.println(stu));

}

（**2**） 遍历 **List\<**对象类型**\>**

接口方法：

List\<Student\> selectStudentForList2(List\<Student\> stuList);

mapper 文件：

\<**select id="selectStudentForList2"
resultType="com.xqc.domain.Student"**\>

select id,name,email,age from student

\<**if test="list !=null and list.size \> 0 "**\>

where id in

\<**foreach collection="list" open="(" close=")"** **item="stuobject"
separator=","**\>

\#{stuobject.id}

\</**foreach**\>

\</**if**\>

\</**select**\>

测试方法：

\@Test

**public void** testSelectForList2() {

List\<Student\> list = **new** ArrayList\<\>();

Student s1 = **new** Student();

s1.setId(1002);

list.add(s1);

s1 = **new** Student();

s1.setId(1005);

list.add(s1);

List\<Student\> studentList = **studentDao**.selectStudentForList2(list);

studentList.forEach( stu -\> System.**out**.println(stu));

}

## 4.5 \<sql/\>与\<include/\>

\<sql/\>标签用于定义 SQL 片断，以便其它 SQL 标签复用。而其它标签使用该 SQL
片断，需要使用

\<include/\>子标签。

## 4.6 \<choose/\>，\<When\>

choose 标签是按顺序判断其内部 when 标签中的 test 条件出否成立，如果有一个成立，则
choose 结束。当 choose 中所有 when 的条件都不满则时，则执行 otherwise 中的 sql

## 4.7\<trim\>

一般用于去除 sql 语句中多余的 and 关键字，逗号，或者给 sql 语句前拼接
“where“、“set“以及“values(“
等前缀，或者添加“)“等后缀，可用于选择性插入、更新、删除或者条件查询等操作

| **属性**        | **描述**                                                                                                                                                  |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| prefix          | 给 sql 语句拼接的前缀                                                                                                                                     |
| suffix          | 给 sql 语句拼接的后缀                                                                                                                                     |
| prefixOverrides | 去除 sql 语句前面的关键字或者字符，该关键字或者字符由 prefixOverrides 属性指定，假设该属性指定为"AND"，当 sql 语句的开头为"AND"，trim 标签将会去除该"AND" |
| suffixOverrides | 去除 sql 语句后面的关键字或者字符，该关键字或者字符由 suffixOverrides 属性指定                                                                            |

\<select id="findActiveBlogLike"

resultType="Blog"\>

SELECT \* FROM BLOG

WHERE

\<if test="state != null"\>

state = \#{state}

\</if\>

\<if test="title != null"\>

AND title like \#{title}

\</if\>

\<if test="author != null and author.name != null"\>

AND author_name like \#{author.name}

\</if\>

\</select\>

如果都没匹配上：SELECT \* FROM BLOG WHERE

如果仅有第二个匹配上：SELECT \* FROM BLOG WHERE AND title like ‘someTitle’

你也可以使用 where 标签去完成该问题

## 4.8\<![CDATA[ ]]\>

在 CDATA 内部的所有内容都会被解析器忽略。

## 4.9：Begin And

# 第 5 章 MyBatis 配置文件

## 5.1 主配置文件

之前项目中使用的 mybatis.xml 是主配置文件。

主配置文件特点：

1. xml 文件，需要在头部使用约束文件

\*\<?**\*xml version="1.0" encoding="UTF-8"** _?\>_

**\<!DOCTYPE configuration**

**PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd"\>**

2.根元素，\<**configuration**\>

3.主要包含内容：

\<!-- settings 是 MyBatis 中全局的调整设置，它们会改变 MyBatis
的运行时行为,应谨慎设置 --\>

\<settings\>

\<!-- 该配置影响的所有映射器中配置的缓存的全局开关。默认值 true --\>

\<setting name="cacheEnabled" value="true"/\>

\<!--延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。
特定关联关系中可通过设置 fetchType 属性来覆盖该项的开关状态。默认值 false --\>

\<setting name="lazyLoadingEnabled" value="true"/\>

\<!-- 是否允许单一语句返回多结果集（需要兼容驱动）。 默认值 true --\>

\<setting name="multipleResultSetsEnabled" value="true"/\>

\<!-- 使用列标签代替列名。不同的驱动在这方面会有不同的表现，
具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。默认值 true
--\>

\<setting name="useColumnLabel" value="true"/\>

\<!-- 允许 JDBC 支持自动生成主键，需要驱动兼容。 如果设置为 true
则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如
Derby）。 默认值 false --\>

\<setting name="useGeneratedKeys" value="false"/\>

\<!-- 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL
只会自动映射没有定义嵌套结果集映射的结果集。 FULL
会自动映射任意复杂的结果集（无论是否嵌套）。 --\>

\<!-- 默认值 PARTIAL --\>

\<setting name="autoMappingBehavior" value="PARTIAL"/\>

\<setting name="autoMappingUnknownColumnBehavior" value="WARNING"/\>

\<!-- 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE
执行器会重用预处理语句（prepared statements）； BATCH
执行器将重用语句并执行批量更新。默认 SIMPLE --\>

\<setting name="defaultExecutorType" value="SIMPLE"/\>

\<!-- 设置超时时间，它决定驱动等待数据库响应的秒数。 --\>

\<setting name="defaultStatementTimeout" value="25"/\>

\<setting name="defaultFetchSize" value="100"/\>

\<!-- 允许在嵌套语句中使用分页（RowBounds）默认值 False --\>

\<setting name="safeRowBoundsEnabled" value="false"/\>

\<!-- 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN
到经典 Java 属性名 aColumn 的类似映射。 默认 false --\>

\<setting name="mapUnderscoreToCamelCase" value="false"/\>

\<!-- MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular
references）和加速重复嵌套查询。

默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。

若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlSession
的不同调用将不会共享数据。 --\>

\<setting name="localCacheScope" value="SESSION"/\>

\<!-- 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。
某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR
或 OTHER。 --\>

\<setting name="jdbcTypeForNull" value="OTHER"/\>

\<!-- 指定哪个对象的方法触发一次延迟加载。 --\>

\<setting name="lazyLoadTriggerMethods"
value="equals,clone,hashCode,toString"/\>

\</settings\>

## 5.2 dataSource 标签

Mybatis 中访问数据库，可以连接池技术，但它采用的是自己的连接池技术。在 Mybatis
的 mybatis.xml 配置文件中，通过\<dataSource type=”pooled”\>来实现 Mybatis
中连接池的配置。

5.2.1 dataSource 类型

Mybatis 将数据源分成三类

UNPOOLED 不使用连接池的数据源

POOLED 使用连接池的数据源

JNDI 使用 JNDI 实现的数据源

其中 UNPOOLED ,POOLED 数据源实现了 javax.sq.DataSource 接口， JNDI
和前面两个实现方式不同，

5.2.2 dataSource 配置

\<property name="driver" value="com.mysql.jdbc.Driver"/\>

\<property name="url" value="jdbc:mysql://localhost:3306/ssm?charset=utf-8"/\>

\<property name="username" value="root"/\>

\<property name="password" value="123456"/\>

\</**dataSource**\>

MyBatis 在初始化时，根据\<dataSource\>的 type 属性来创建相应类型的的数据源
DataSource，即： type=”POOLED”：MyBatis 会创建 PooledDataSource 实例

type=”UNPOOLED” ： MyBatis 会创建 UnpooledDataSource 实例 type=”JNDI”：MyBatis
会从 JNDI 服务上查找 DataSource 实例，然后返回使用

## 5.3 事务

（**1**） 默认需要手动提交事务

Mybatis 框架是对 JDBC 的封装，所以 Mybatis 框架的事务控制方式，本身也是用 JDBC
的 Connection 对象的 commit(), rollback() .

Connection 对象的 setAutoCommit()方法来设置事务提交方式的。自动提交和手工提交、

该标签用于指定 MyBatis 所使用的事务管理器。MyBatis
支持两种事务管理器类型：**JDBC** 与 **MANAGED**。

➢JDBC：使用 JDBC 的事务管理机制。即，通过 Connection 的 commit()方法提交，通过
rollback()方法回滚。但默认情况下，MyBatis
将自动提交功能关闭了，改为了手动提交。即程序中需要显式的对事务进行提交或回滚。从日志的输出信息中可以看到。

![](media/ca43a096480c53e5266c9b5592e41124.jpg)

MANAGED：由容器来管理事务的整个生命周期（如 Spring 容器）。

（**2**） 自动提交事务

设置自动提交的方式，factory 的 openSession() 分为有参数和无参数的。

![](media/397ffbb68d41d495b24c5f5ec4bd78f1.jpg)

有参数为 true，使用自动提交，可以修改 MyBatisUtil 的 getSqlSession()方法。
session = _factory_.openSession(**true**);

再执行 insert 操作，无需执行 session.commit(),事务是自动提交的

## 5.4 使用数据库属性配置文件

为 了方便对数据库连接的管理，DB
连接四要素数据一般都是存放在一个专门的属性文件中的。MyBatis
主配置文件需要从这个属性文件中读取这些数据。

步骤：

（**1**） 在 **classpath** 路径下，创建 **properties** 文件

在 resources 目录创建 jdbc.properties 文件，文件名称自定义。

![](media/e9a78db108ab047b4f3c5301d102502f.jpg)

（**2**） 使用 **properties** 标签

![](media/b57690224d6d63fb8faccbb6b1530134.jpg)

\<**dataSource type="POOLED"**\>

**\<!--**使用**properties**文件**:** 语法 **\${key}--\>**

\<**property name="driver" value="\${jdbc.driver}"**/\>

\<**property name="url" value="\${jdbc.url}"**/\>

\<**property name="username" value="\${jdbc.username}"**/\>

\<**property name="password" value="\${jdbc.password}"**/\>

\</**dataSource**\>

5.5 typeAliases（类型别名）

Mybatis 支持默认别名，我们也可以采用自定义别名方式来开发，主要使用在\<select
resultType=”别名”\> mybatis.xml 主配置文件定义别名：

\<**typeAliases**\>

**\<!--**

定义单个类型的别名 **type:**类型的全限定名称 **alias:**自定义别名

**--\>**

\<typeAlias type="com.xqc.domain.Student" alias="mystudent"/\>

**\<!--**

批量定义别名，扫描整个包下的类，别名为类名（首字母大写或小写都可以）
**name:**包名

**--\>**

\<package name="com.xqc.domain"/\>

\<**package name="...**其他包**"**/\>

\</**typeAliases**\>

mapper.xml 文件，使用别名表示类型

**\<select id="selectStudents" resultType="mystudent"\>** select
id,name,email,age from student **\</select\>**

## 5.6 mappers（映射器）

（1） \<mapper resource=" " /\>

使用相对于类路径的资源,从 classpath 路径查找文件

例如：\<mapper resource="com/xqc/dao/StudentDao.xml" /\>

（2） \<package name=""/\>

指定包下的所有 Dao 接口

如：\<package name="com.xqc.dao"/\> 注意：此种方法要求 Dao 接口名称和
mapper 映射文件名称相同，且在同一个目录中。

# 第 7 章 扩展

## 7.1 PageHelper

### 7.1.1 Mybatis 通用分页插件

<https://github.com/pagehelper/Mybatis-PageHelper>

PageHelper 支持多种数据库

### **7.1.2 基于 PageHelper 分页：**

实现步骤

（1） maven 坐标

```xml
<dependency>
  <groupId>com.github.pagehelper</groupId>
  <artifactId>pagehelper</artifactId>
  <version>5.1.10</version>
</dependency>

```

（2） 加入 plugin 配置

在**\<environments\>**之前加入

```xml
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor" />
</plugins>
```

（3）PageHelper 对象

查询语句之前调用 PageHelper.startPage 静态方法。除了 PageHelper.startPage 方法外，还提供了类似用法的 PageHelper.offsetPage 方法。

在你需要进行分页的 MyBatis 查询方法前调用 PageHelper.startPage 静态方法即可，紧跟在这个方法后的第一个 MyBatis 查询方法会被进行分页。

```java
 @Test
public void testSelect() throws IOException {
    //获取第1页，3条内容
    PageHelper.stratPage(1,3);
    List<Student> studentList = studentDao.selectStudents();
    studentList.forEach( stu -> System.out.println(stu));
}

```

当 ResultMap 去重遇到 PageHelper 的时候不支持

其分页原理是物理分页，即在执行 sql 语句前进行过滤

对于 oracle，是在外层套一个

```sql
select * from{
	……
}where rownum <=20
```

对于 mysql，在外层套一个

```sql
select... from ...where...limit  x, y
```

# 第八章：Mybatis 连接池与事务深入

## 1：连接池

1：分类

![](media/346d0ec61b2c812043a4df85571e4cd1.png)

可以看出 Mybatis 将它自己的数据源分为三类：

UNPOOLED 不使用连接池的数据源

POOLED 使用连接池的数据源

JNDI 使用 JNDI 实现的数据源

2：数据源配置

我们的数据源配置就是在 SqlMapConfig.xml 文件中，具体配置如下：

\<!-- 配置数据源（连接池）信息 --\>

\<dataSource type=\*"**_POOLED_**"\*\>

\<property name=_"driver"_ value=_"\${jdbc.driver}"_/\>

\<property name=_"url"_ value=_"\${jdbc.url}"_/\>

\<property name=_"username"_ value=_"\${jdbc.username}"_/\>

\<property name=_"password"_ value=_"\${jdbc.password}"_/\>

\</dataSource\>

MyBatis 在初始化时，根据\<dataSource\>的 type 属性来创建相应类型的的数据源 DataSource，即：

type=”POOLED”：MyBatis 会创建 PooledDataSource 实例

type=”UNPOOLED” ： MyBatis 会创建 UnpooledDataSource 实例

type=”JNDI”：MyBatis 会从 JNDI 服务上查找 DataSource 实例，然后返回使用

# 第九章：多表查询

9.1:一对一

用户类：

账号类：

Public class Account implement Serializable{

Private Interger id;

Private Integer Uid;

Private Doublle money;

Private User user;

//set 和 get 方法

}

Dao 方法

List\<Account\> findAll();

Mapper 文件

![](media/798c39035a9d620248ec2fa3f486146b.png)

## 9.1：一对多

一个用户有多个账号，账号表有外键用户 id

查询所有用户信息及用户关联的账户信息，查询过程中如果用户没有账户信息，此时也要将用户信息查询出来，左连接较为合适。

SQL 语句：

SELECT u.\*,acc.id id,acc.uis,acc.money FROM user u LEFT JOIN account acc ON
u.id = acc.uid

1：在 User 类中加入 List\<Account\>

![](media/7216b35ef8e2c30f54640c9a84c086b2.png)

2：DAO 加入查询方法

![](media/4073423b890c22675633a4b67c613c45.png)

3：Mapper 文件

![](media/0a4e84cd65fbef90c05b1da3cbf5ed6d.png)

![](media/a15190fe81f0cf703e94bed640a74a71.png)

Collection:部分定义了用户关联的账户信息

Property：关联结果集存储在 User 对象上的那个属性

OfType：指定关联查询的结果集中的对象类型即 List 中的对象类型，此处可以使用别名，也可以使用权限定名

4：测试

![](media/0d7ff09775c7223c91452707e0d5a613.png)

## 9.2：多对多查询

![](media/042e6b4ebe197268206bcc2e094c1bfc.png)

查询角色我们要用到 Role 表，但角色分配的用户信息我们并不能直接找到用户信息，而是要通过中间表（User_ROLE）才能关联到用户信息，

1：实体

Public class Role implement Serializable{

……

//多对多的关系映射，一个角色可以赋予多个用户

Private List\<User\> users;

}

2：Dao

![](media/549e3305b54f6f6385bafca91f28bdcb.png)

3：

![](media/c7d29bb589b5dd804bb939d0c7202c18.png)

![](media/3242f174bc33964dcd69871cedba9f3f.png)

## 1：懒加载

按需加载，我们需要什么的时候再去进行什么操作。而且先从单表查询，需要时再从关联表去关联查询，能大大提高数据库性能，

在 mybatis 中，resultMap 可以实现高级映射(使用 association、collection 实现一对一及一对多映射)，association、collection 具备延迟加载功能。

\<resultMap id="BaseResultMap"
type="com.agesun.attendance.privilege.provider.model.Org"\>  
\<id column="org_id" jdbcType="INTEGER" property="orgId" /\>  
\<result column="parent_id" jdbcType="INTEGER" property="parentId" /\>  
\<result column="org_name" jdbcType="VARCHAR" property="orgName" /\>  
\<result column="state" jdbcType="INTEGER" property="state" /\>  
\<result column="orgFullName" jdbcType="VARCHAR" property="orgFullName" /\>  
\<collection property="psList" column="org_id" fetchType="lazy"
select="com.agesun.attendance.privilege.provider.mapper.PersonMapper.selectByOrgId"\>  
\</collection\>

//单个 resultMap 中的懒加载设置 lazy 为懒加载,不调用（get()）,不从数据查询  
\</resultMap\>

在 mybatis 配置文件 **mybatis-configuration.xml**中，配置懒加载

\<!-- 开启懒加载配置 --\>

\<settings\>

\<!-- 全局性设置懒加载。如果设为‘false'，则所有相关联的都会被初始化加载。 --\>
//可以配置 lazyLoadingEnabled 值为 true，不设置 aggressiveLazyLoading，为全局设置  
\<setting name="lazyLoadingEnabled" value="true"/\>  
\<!--
当设置为‘true'的时候，懒加载的对象可能被任何懒属性全部加载。否则，每个属性都按需加载。
--\>

\<setting name="aggressiveLazyLoading" value="false"/\>

\</settings\>

# 第 10 章：延迟加载策略

通过前面的学习，我们已经掌握了 Mybatis 中一对一，一对多，多对多关系的配置及实现，可以实现对象的关联查询。实际开发过程中很多时候我们并不需要总是在加载用户信息时就一定要加载他的账户信息。此时就是我们所说的延迟加载。

## 1 ：简介

延迟加载：

就是在需要用到数据时才进行加载，不需要用到数据时就不加载数据。延迟加载也称懒加载.

好处：先从单表查询，需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表速度要快。

坏处：

因为只有当需要用到数据时，才会进行数据库查询，这样在大批量数据查询时，因为查询工作也要消耗时间，所以可能造成用户等待时间变长，造成用户体验下降。

需求：

查询账户(Account)信息并且关联查询用户(User)信息。如果先查询账户(Account)信息即可满足要求，当我们需要查询用户(User)信息时再查询用户(User)信息。把对用户(User)信息的按需去查询就是延迟加载。

mybatis 第三天实现多表操作时，我们使用了 resultMap 来实现一对一，一对多，多对多关系的操作。主要是通过 association、collection 实现一对一及一对多映射。association、collection 具备延迟加载功能。

### 1.1 使用 assocation 实现延迟加载

需求：

查询账户信息同时查询用户信息。

账户的持久层**DAO**接口

/\*

\* \<p\>Title: IAccountDao\</p\>

\* \<p\>Description: 账户的持久层接口\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public interface** IAccountDao {

/\*\*

\* 查询所有账户，同时获取账户的所属用户名称以及它的地址信息

\* **\@return**

\*/

List\<Account\> findAll();

}

账户持久层映射

\<?xml version=_"1.0"_ encoding=_"UTF-8"_?\>

\<!DOCTYPE mapper

PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"

"http://mybatis.org/dtd/mybatis-3-mapper.dtd"\>

\<mapper namespace=_"com.xqc.dao.IAccountDao"_\>

\<!-- 建立对应关系 --\>

\<resultMap type=_"account"_ id=_"accountMap"_\>

\<id column=_"aid"_ property=_"id"_/\>

\<result column=_"uid"_ property=_"uid"_/\>

\<result column=_"money"_ property=_"money"_/\>

\<!-- 它是用于指定从表方的引用实体属性的 --\>

\<association property=_"user"_ javaType=_"user"_

select=_"com.xqc.dao.IUserDao.findById"_

column=_"uid"_\>

\</association\>

\</resultMap\>

\<select id=_"findAll"_ resultMap=_"accountMap"_\>

select \* from account

\</select\>

\</mapper\>

**select**： 填写我们要调用的 select 映射的 id

**column** ： 填写我们要传递给 select 映射的参数

用户的业务接口

/\*

\* \<p\>Title: IUserDao\</p\>

\* \<p\>Description: 用户的业务层接口\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public interface** IUserDAO

{

User findById(Integer userId);

}

\<?xml version=_"1.0"_ encoding=_"UTF-8"_?\>

\<!DOCTYPE mapper

PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"

"http://mybatis.org/dtd/mybatis-3-mapper.dtd"\>

\<mapper namespace=_"com.xqc.dao.IUserDao"_\>

\<!-- 根据 id 查询 --\>

\<select id=_"findById"_ resultType=_"user"_ parameterType=_"int"_ \>

select \* from user where id = \#{uid}

\</select\>

\</mapper\>

开启**Mybatis**的延迟加载策略

进入 Mybaits 的官方文档，找到 settings 的说明信息：

我们需要在 Mybatis 的配置文件 SqlMapConfig.xml 文件中添加延迟加载的配置。

\<!-- 开启延迟加载的支持 --\>

\<settings\>

\<setting name=_"lazyLoadingEnabled"_ value=_"true"_/\>

\<setting name=_"aggressiveLazyLoading"_ value=_"false"_/\>

\</settings\>

编写测试只查账户信息不查用户信息。

/\*\*

\*

\* \<p\>Title: MybastisCRUDTest\</p\>

\* \<p\>Description: 一对多账户的操作\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public class** AccountTest {

**private** InputStream in ;

**private** SqlSessionFactory factory;

**private** SqlSession session;

**private** IAccountDao accountDao;

\@Test

**public void** testFindAll() {

//6.执行操作

List\<Account\> accounts = accountDao.findAll();

}

\@Before//在测试方法执行之前执行

**public void** init()**throws** Exception {

//1.读取配置文件

in = Resources._getResourceAsStream_("SqlMapConfig.xml");

//2.创建构建者对象

SqlSessionFactoryBuilder builder = **new** SqlSessionFactoryBuilder();

//3.创建 SqlSession 工厂对象

factory = builder.build(in);

//4.创建 SqlSession 对象

session = factory.openSession();

//5.创建 Dao 的代理对象

accountDao = session.getMapper(IAccountDao.**class**);

}

\@After//在测试方法执行完成之后执行

**public void** destroy() **throws** Exception{

//7.释放资源

session.close();

in.close();

}

}

测试结果如下：

我们发现，因为本次只是将 Account 对象查询出来放入 List 集合中，并没有涉及到 User 对象，所以就没有发出 SQL 语句查询账户所关联的 User 对象的查询。

### 1.2 使用 Collection 实现延迟加载

同样我们也可以在一对多关系配置的\<collection\>结点中配置延迟加载策略。

\<collection\>结点中也有 select 属性，column 属性。

需求：

完成加载用户对象时，查询该用户所拥有的账户信息。

在 User 实体类中加入 List\<Account\>属性

/\*\*

\*

\* \<p\>Title: User\</p\>

\* \<p\>Description: 用户的实体类\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public class** User **implements** Serializable {

**private** Integer id;

**private** String username;

**private** Date birthday;

**private** String sex;

**private** String address;

**private** List\<Account\> accounts;

**public** List\<Account\> getAccounts() {

**return** accounts;

}

**public void** setAccounts(List\<Account\> accounts) {

**this**.accounts = accounts;

}

**public** Integer getId() {

**return** id;

}

**public void** setId(Integer id) {

**this**.id = id;

}

**public** String getUsername() {

**return** username;

}

**public void** setUsername(String username) {

**this**.username = username;

}

**public** Date getBirthday() {

**return** birthday;

}

**public void** setBirthday(Date birthday) {

**this**.birthday = birthday;

}

**public** String getSex() {

**return** sex;

}

**public void** setSex(String sex) {

**this**.sex = sex;

}

**public** String getAddress() {

**return** address;

}

**public void** setAddress(String address) {

**this**.address = address;

}

\@Override

**public** String toString() {

**return** "User [id=" + id + ", username=" + username + ", birthday=" +
birthday + ", sex=" + sex + ", address="

\+ address + "]";

}

}

### 1.3 编写用户和账户持久层接口的方法

/\*\*

\* 查询所有用户，同时获取出每个用户下的所有账户信息

\* \@return

\*/

List\<User\> findAll();

/\*\*

\* 根据用户 id 查询账户信息

\* **\@param** uid

\* **\@return**

\*/

List\<Account\> findByUid(Integer uid);

编写用户持久层映射配置

\<resultMap type=_"user"_ id=_"userMap"_\>

\<id column=_"id"_ property=_"id"_\>\</id\>

\<result column=_"username"_ property=_"username"_/\>

\<result column=_"address"_ property=_"address"_/\>

\<result column=_"sex"_ property=_"sex"_/\>

\<result column=_"birthday"_ property=_"birthday"_/\>

\<!-- collection 是用于建立一对多中集合属性的对应关系

ofType 用于指定集合元素的数据类型

select 是用于指定查询账户的唯一标识（账户的 dao 全限定类名加上方法名称）

column 是用于指定使用哪个字段的值作为条件查询

\--\>

\<collection property=_"accounts"_ ofType=_"account"_

select=_"com.xqc.dao.IAccountDao.findByUid"_

column=_"id"_\>

\</collection\>

\</resultMap\>

\<!-- 配置查询所有操作 --\>

\<select id=_"findAll"_ resultMap=_"userMap"_\>

select \* from user

\</select\>

**\<collection\>**标签：

主要用于加载关联的集合对象

**select**属性：

用于指定查询 account 列表的 sql 语句，所以填写的是该 sql 映射的 id

**column**属性：

用于指定 select 属性的 sql 语句的参数来源，上面的参数来自于 user 的 id 列，所以就写成 id 这一个字段名了

编写账户持久层映射配置

\<!-- 根据用户 id 查询账户信息 --\>

\<select id=_"findByUid"_ resultType=_"account"_ parameterType=_"int"_\>

select \* from account where uid = \#{uid}

\</select\>

测试只加载用户信息

/\*\*

\*

\* \<p\>Title: MybastisCRUDTest\</p\>

\* \<p\>Description: 一对多的操作\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public class** UserTest {

**private** InputStream in ;

**private** SqlSessionFactory factory;

**private** SqlSession session;

**private** IUserDao userDao;

\@Test

**public void** testFindAll() {

//6.执行操作

List\<User\> users = userDao.findAll();

}

\@Before//在测试方法执行之前执行

**public void** init()**throws** Exception {

//1.读取配置文件

in = Resources._getResourceAsStream_("SqlMapConfig.xml");

//2.创建构建者对象

SqlSessionFactoryBuilder builder = **new** SqlSessionFactoryBuilder();

//3.创建 SqlSession 工厂对象

factory = builder.build(in);

//4.创建 SqlSession 对象

session = factory.openSession();

//5.创建 Dao 的代理对象

userDao = session.getMapper(IUserDao.**class**);

}

\@After//在测试方法执行完成之后执行

**public void** destroy() **throws** Exception{

session.commit();

//7.释放资源

session.close();

in.close();

}

}

测试结果如下：

我们发现并没有加载 Account 账户信息。

# 第 11 章 Mybatis 缓存

像大多数的持久化框架一样，Mybatis 也提供了缓存策略，通过缓存策略来减少数据库的查询次数，从而提高性能。

Mybatis 中缓存分为一级缓存，二级缓存。

![](media/5109d80f05a963e35e69214a91c8be8a.png)

一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session
flush 或 close 之后，该 Session 中的所有 Cache 就将清空。

二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储，不同在于
其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。

对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存 Namespaces)的进行了
C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。

**但是二级缓存会带来问题：**

因为二级缓存时保存在 Mapper 对象中的，如果一个 user 的操作对应了两个 mapper，Amapper.xml 修改后 Bmapper.xml 是无法感知到的。

例如：

对于 tableA 与 tableB 的操作定义在两个 Mapper 中，分别叫做 MapperA 与 MapperB，即它们属于两个命名空间，如果此时启用缓存:

MapperA 中执行上述 sql 语句查询这 6 个字段

tableB 更新了 col1 与 col2 两个字段

MapperA 再次执行上述 sql 语句查询这 6 个字段〈前提是没有执行过任何 insert、delete、update 操作)
此时问题就来了，即使第(2）步 tableB 更新了 col1 与 col2 两个字段，第(3)步 MapperA 走二级缓存查询到的这 6 个字段依然是原来的这 6 个字段的值

## 1：Mybatis 一级缓存

### 1.1 证明一级缓存的存在

一级缓存是 SqlSession 级别的缓存，只要 SqlSession 没有 flush 或 close，它就存在。

编写用户持久层**Dao**接口

```java
public interface IUserDao {
/**
* 根据id查询
* @param userId
* @return
*/
User findById(Integer userId);
}
```

编写用户持久层映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xqc.dao.IUserDao">
<!-- 根据id查询 -->
<select id="findById" resultType="UsEr" parameterType="int" useCache="true">
select * from user where id = #{uid}
</select>
</mapper>

```

编写测试方法

```java
/**
*
* <p>Title: MybastisCRUDTest</p>
* <p>Description: 一对多的操作</p>
* <p>Company: http://www.xqc.com/ </p>
*/
public class UserTest {
    private InputStream in ;
    private SqlSessionFactory factory;
    private SqlSession session;
    private IUserDao userDao;
    @Test
    public void testFindById() {
        //6.执行操作
        User user = userDao.findById(41);
        System.out.println("第一次查询的用户："+user);
        User user2 = userDao.findById(41);
        System.out.println("第二次查询用户："+user2);
        System.out.println(user == user2);
    }

    @Before//在测试方法执行之前执行
    public void init()throws Exception {
        //1.读取配置文件
        in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建构建者对象
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        //3.创建SqlSession工厂对象
        factory = builder.build(in);
        //4.创建SqlSession对象
        session = factory.openSession();
        //5.创建Dao的代理对象
        userDao = session.getMapper(IUserDao.class);
    }

    @After//在测试方法执行完成之后执行
    public void destroy() throws Exception{
        //7.释放资源
        session.close();
        in.close();
    }
}

```

测试结果如下：

我们可以发现，虽然在上面的代码中我们查询了两次，但最后只执行了一次数据库操作，这就是 Mybatis 提供给我们的一级缓存在起作用了。因为一级缓存的存在，导致第二次查询 id 为 41 的记录时，并没有发出 sql 语句从数据库中查询数据，而是从一级缓存中查询。

### 1.2 一级缓存的分析

一级缓存是 SqlSession 范围的缓存，当调用 SqlSession 的修改，添加，删除，commit()，close()方法时，就会清空一级缓存。

第一次发起查询用户 id 为 1 的用户信息，先去找缓存中是否有 id 为 1 的用户信息，如果没有，从数据库查询用户信息。

得到用户信息，将用户信息存储到一级缓存中。

如果 sqlSession 去执行 commit 操作（执行插入、更新、删除），清空 SqlSession 中的一级缓存，这样做的目的为了让缓存中存储的是最新的信息，避免脏读。

第二次发起查询用户 id 为 1 的用户信息，先去找缓存中是否有 id 为 1 的用户信息，缓存中有，直接从缓存中获取用户信息。

MyBatis 的一级缓存是默认开启的，不需要任何的配置。

### 1.3 测试一级缓存的清空

测试一级缓存

```java
public class UserTest {
    private InputStream in ;
    private SqlSessionFactory factory;
    private SqlSession session;
    private IUserDao userDao;
    @Test
    public void testFindById() {
        //6.执行操作

        User user = userDao.findById(41);
        System.out.println("第一次查询的用户："+user);
        User user2 = userDao.findById(41);
        System.out.println("第二次查询用户："+user2);
        System.out.println(user == user2);
    }

    @Before//在测试方法执行之前执行
    public void init()throws Exception {
        //1.读取配置文件
        in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建构建者对象
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        //3.创建SqlSession工厂对象
        factory = builder.build(in);
        //4.创建SqlSession对象
        session = factory.openSession();
        //5.创建Dao的代理对象
        userDao = session.getMapper(IUserDao.class);
    }

    @After//在测试方法执行完成之后执行
    public void destroy() throws Exception{
        //7.释放资源
        session.close();
        in.close();
    }
}

```

测试缓存同步

```java
@Test
public void testClearlCache(){
    //1.根据id查询用户
    User user1 = userDao.findById(41);
    System.out.println(user1);
    //2.更新用户信息
    user1.setUsername("update user clear cache");
    user1.setAddress("北京市海淀区");
    userDao.updateUser(user1);
    //3.再次查询id为41的用户
    User user2 = userDao.findById(41);
    System.out.println(user2);
    System.out.println(user1 == user2);
}
```

当执行 sqlSession.close()后，再次获取 sqlSession 并查询 id=41 的 User 对象时，又重新执行了 sql 语句，从数据库进行了查询操作。

### 1.4：总结

MyBatis 的一级缓存是默认开启的，不需要任何的配置。

一级缓存的生命周期有多长？

1. MyBatis 在开启一个数据库会话时，会 创建一个新的 SqlSession 对象，SqlSession 对象中会有一个新的 Executor 对象，Executor 对象中持有一个新的 PerpetualCache 对象；当会话结束时，SqlSession 对象及其内部的 Executor 对象还有 PerpetualCache 对象也一并释放掉。
2. 如果 SqlSession 调用了 close()方法，会释放掉一级缓存 PerpetualCache 对象，一级缓存将不可用；
3. 如果 SqlSession 调用了 clearCache()，会清空 PerpetualCache 对象中的数据，但是该对象仍可使用；
4. SqlSession 中执行了任何一个 update 操作(update()、delete()、insert()) ，都会清空 PerpetualCache 对象的数据，但是该对象可以继续使用；

一级缓存的不足：

使用一级缓存的时候，因为缓存不能跨会话共享，不同的会话之间对于相同的数据可能有不一样的缓存。在有多个会话或者分布式环境下，会存在脏数据的问题。如果要解决这个问题，就要用到二级缓存。MyBatis 一级缓存（MyBaits 称其为 Local Cache）无法关闭，但是有两种级别可选：

- session 级别的缓存，在同一个 sqlSession 内，对同样的查询将不再查询数据库，直接从缓存中。

- statement 级别的缓存，避坑： 为了避免这个问题，可以将一级缓存的级别设为 statement 级别的，这样每次查询结束都会清掉一级缓存。

## 2 Mybatis 二级缓存

二级缓存是 mapper 映射级别的缓存，多个 SqlSession 去操作同一个 Mapper 映射的 sql 语句，多个 SqlSession 可以共用二级缓存，二级缓存是跨 SqlSession 的。二级缓存是用来解决以及缓存不能跨会话共享问题的

### 2.1 二级缓存结构图

![](media/2c94c90b327a40bba713e49ee038bc2d.png)

首先开启 mybatis 的二级缓存。

sqlSession1 去查询用户信息，查询到用户信息会将查询数据存储到二级缓存中。

如果 SqlSession3 去执行相同 mapper 映射下 sql，执行 commit 提交，将会清空该 mapper 映射下的二级缓存区域的数据。

sqlSession2 去查询与 sqlSession1 相同的用户信息，首先会去缓存中找是否存在数据，如果存在直接从缓存中取出数据。

### 2.2 二级缓存的开启与关闭

第一步：在**SqlMapConfig.xml**文件开启二级缓存

```xml
<settings>
<!-- 开启二级缓存的支持 -->
<setting name="cacheEnabled" value="true"/>
</settings>
```

因 cacheEnabled 的取值默认就为 true，所以这一步可以省略不配置。为 true 代表开启二级缓存；为 false 代表不开启二级缓存。

第二步：配置相关的**Mapper**映射文件，在 Mapping 文件中打上<cache>标签

\<cache\>标签表示当前这个 mapper 映射将使用二级缓存，区分的标准就看 mapper 的 namespace 值。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xqc.dao.IUserDao">
<!-- 开启二级缓存的支持 -->
<cache type="org.apache.ibatis.cache.impl.PerpetualCache"
    size="1024"
eviction="LRU"
flushInterval="120000"
readOnly="false"/>
</mapper>

```

这个简单语句的效果如下:

- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。

可用的清除策略有：

- `LRU` – 最近最少使用：移除最长时间不被使用的对象。
- `FIFO` – 先进先出：按对象进入缓存的顺序来移除它们。
- `SOFT` – 软引用：基于垃圾回收器状态和软引用规则移除对象。
- `WEAK` – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。

默认的清除策略是 LRU。

flushInterval（刷新间隔）属性可以被设置为任意的正整数，设置的值应该是一个以毫秒为单位的合理时间量。 默认情况是不设置，也就是没有刷新间隔，缓存仅仅会在调用语句时刷新。

size（引用数目）属性可以被设置为任意正整数，要注意欲缓存对象的大小和运行环境中可用的内存资源。默认值是 1024。

readOnly（只读）属性可以被设置为 true 或 false。只读的缓存会给所有调用者返回缓存对象的相同实例。 因此这些对象不能被修改。这就提供了可观的性能提升。而可读写的缓存会（通过序列化）返回缓存对象的拷贝。 速度上会慢一些，但是更安全，因此默认值是 false。

第三步：配置**statement**上面的**useCache**属性

```xml
<!-- 根据id查询 -->
<select id="findById" resultType="user" parameterType="int" useCache="true">
select * from user where id = #{uid}
</select>
```

将 UserDao.xml 映射文件中的\<select\>标签中设置 useCache=”true”代表当前这个 statement 要使用二级缓存，如果不使用二级缓存可以设置为 false。

注意：针对每次查询都需要最新的数据 sql，要设置成 useCache=false，禁用二级缓存。

### 2.3 二级缓存测试

```java
public class SecondLevelCacheTest {
private InputStream in;
private SqlSessionFactory factory;
@Before//用于在测试方法执行之前执行
public void init()throws Exception{
//1.读取配置文件，生成字节输入流
in = Resources.getResourceAsStream("SqlMapConfig.xml");
//2.获取SqlSessionFactory
factory = new SqlSessionFactoryBuilder().build(in);
}
@After//用于在测试方法执行之后执行
public void destroy()throws Exception{
in.close();
}

```

测试二级缓存

```java
@Test
public void testFirstLevelCache(){
SqlSession sqlSession1 = factory.openSession();
IUserDao dao1 = sqlSession1.getMapper(IUserDao.class);
User user1 = dao1.findById(41);
System.out.println(user1);
sqlSession1.close();//一级缓存消失
SqlSession sqlSession2 = factory.openSession();
IUserDao dao2 = sqlSession2.getMapper(IUserDao.class);
User user2 = dao2.findById(41);
System.out.println(user2);
sqlSession2.close();
System.out.println(user1 == user2);
}
}

```

经过上面的测试，我们发现执行了两次查询，并且在执行第一次查询后，我们关闭了一级缓存，再去执行第二次查询时，我们发现并没有对数据库发出 sql 语句，所以此时的数据就只能是来自于我们所说的二级缓存。

### 2.4：总结

当我们在使用二级缓存时，所缓存的类一定要实现 java.io.Serializable 接口，这种就可以使用序列化方式来保存对象。

```java
public class User implements Serializable {
}
```

如果你的 MyBatis 使用了二级缓存，并且你的 Mapper 和 select 语句也配置使用了二级缓存，那么在执行 select 查询的时候，MyBatis 会先从二级缓存中取输入，其次才是一级缓存，即 MyBatis 查询数据的顺序是：二级缓存 —> 一级缓存 —> 数据库。

在什么情况下才有必要去开启二级缓存？

1. 因为所有的增删改都会刷新二级缓存，导致二级缓存失效，所以适合在查询为主的应用中使用，比如历史交易、历史订单的查询。否则缓存就失去了意义。
2. 如果多个 namespace 中有针对于同一个表的操作，比如 Blog 表，如果在一个 namespace 中刷新了缓存，另一个 namespace 中没有刷新，就会出现读到脏数据的情况。所以，推荐在一个 Mapper 里面只操作单表的情况使用。

如果要让多个 namespace 共享一个二级缓存，应该怎么做？跨 namespace 的缓存共享的问题，可以使用<cache-ref>来解决：

```
<cache-ref namespace="com.wuzz.crud.dao.DepartmentMapper" />
```

cache-ref 代表引用别的命名空间的 Cache 配置，两个命名空间的操作使用的是同一个 Cache。在关联的表比较少，或者按照业务可以对表进行分组的时候可以使用。

注意：在这种情况下，多个 Mapper 的操作都会引起缓存刷新，缓存的意义已经不大了.

第三方缓存做二级缓存

除了 MyBatis 自带的二级缓存之外，我们也可以通过实现 Cache 接口来自定义二级缓存。

# 第 6 章：注解开发

## 1：使用案例

1：在 DAO 层添加注解

/\*\*

\* 查询所有用户

\* **\@return**

\*/

**\@Select**("select \* from user")

List\<User\> findAll();

2：修改 SqlMapConfig.xml

\<!-- 告知 mybatis 映射配置的位置 --\>

\<mappers\>

\<mapper class=_"com.xqc.dao.IUserDao"_/\>

\</mappers\>

## 2：CRUD

# 第 12 章：注解开发

## 1: mybatis 的常用注解

**\@Insert:**实现新增

\@Update:实现更新

\@Delete:实现删除

\@Select:实现查询

\@Result:实现结果集封装

\@Results:可以与\@Result 一起使用，封装多个结果集

\@ResultMap:实现引用\@Results 定义的封装

\@One:实现一对一结果集封装

\@Many:实现一对多结果集封装

\@SelectProvider: 实现动态 SQL 映射

\@CacheNamespace:实现注解二级缓存的使用

\@Insert

![](media/88dd004c8f57da65a9fc11abe4a22c5e.png)

\@delete

![](media/2a9530a6848ac161ce64f972956e4bf2.png)

\@update

![](media/1067488368f7456b3a312be4995631db.png)

\@select

![](media/bff2bd9a3b459e64d7430c4b5890d19c.png)

\@one：一对一关系

![](media/392523d66e7d41333202e0bdb07ee6dd.png)

## 2 ：使用 Mybatis 注解实现基本 CRUD

单表的 CRUD 操作是最基本的操作，前面我们的学习都是基于 Mybaits 的映射文件来实现的。

编写实体类

/\*\*

\*

\* \<p\>Title: User\</p\>

\* \<p\>Description: 用户的实体类\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public class** User **implements** Serializable {

**private** Integer userId;

**private** String userName;

**private** Date userBirthday;

**private** String userSex;

**private** String userAddress;

**public** Integer getUserId() {

**return** userId;

}

\@Override

**public** String toString() {

**return** "User [userId=" + userId + ", userName=" + userName + ",
userBirthday=" + userBirthday + ", userSex="

\+ userSex + ", userAddress=" + userAddress + "]";

}

}

注意：

此处我们故意和数据库表的列名不一致。

使用注解方式开发持久层接口

/\*\*

\*

\* \<p\>Title: IUserDao\</p\>

\* \<p\>Description: 用户的持久层接口\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public interface** IUserDao {

/\*\*

\* 查询所有用户

\* **\@return**

\*/

\@Select("select \* from user")

\@Results(id="userMap",

value= {

\@Result(id=**true**,column="id",property="userId"),

\@Result(column="username",property="userName"),

\@Result(column="sex",property="userSex"),

\@Result(column="address",property="userAddress"),

\@Result(column="birthday",property="userBirthday")

})

List\<User\> findAll();

/\*\*

\* 根据 id 查询一个用户

\* **\@param** userId

\* **\@return**

\*/

\@Select("select \* from user where id = \#{uid} ")

\@ResultMap("userMap")

User findById(Integer userId);

/\*\*

\* 保存操作

\* **\@param** user

\* **\@return**

\*/

\@Insert("insert into
user(username,sex,birthday,address)values(\#{username},\#{sex},\#{birthday},\#{address})")

\@SelectKey(keyColumn="id",keyProperty="id",resultType=Integer.**class**,before
= **false**, statement = { "select last_insert_id()" })

**int** saveUser(User user);

/\*\*

\* 更新操作

\* **\@param** user

\* **\@return**

\*/

\@Update("update user set
username=\#{username},address=\#{address},sex=\#{sex},birthday=\#{birthday}
where id =\#{id} ")

**int** updateUser(User user);

/\*\*

\* 删除用户

\* **\@param** userId

\* **\@return**

\*/

\@Delete("delete from user where id = \#{uid} ")

**int** deleteUser(Integer userId);

/\*\*

\* 查询使用聚合函数

\* **\@return**

\*/

\@Select("select count(\*) from user ")

**int** findTotal();

/\*\*

\* 模糊查询

\* **\@param** name

\* **\@return**

\*/

\@Select("select \* from user where username like \#{username} ")

List\<User\> findByName(String name);

}

通过注解方式，我们就不需要再去编写 UserDao.xml 映射文件了。

编写**SqlMapConfig** 配置文件

\<?xml version=_"1.0"_ encoding=_"UTF-8"_?\>

\<!DOCTYPE configuration

PUBLIC "-//mybatis.org//DTD Config 3.0//EN"

"http://mybatis.org/dtd/mybatis-3-config.dtd"\>

\<configuration\>

\<!-- 配置 properties 文件的位置 --\>

\<properties resource=_"jdbcConfig.properties"_\>\</properties\>

\<!-- 配置别名的注册 --\>

\<typeAliases\>

\<package name=_"com.xqc.domain"_/\>

\</typeAliases\>

\<!-- 配置环境 --\>

\<environments default=_"mysql"_\>

\<!-- 配置 mysql 的环境 --\>

\<environment id=_"mysql"_\>

\<!-- 配置事务的类型是 JDBC --\>

\<transactionManager type=_"JDBC"_\>\</transactionManager\>

\<!-- 配置数据源 --\>

\<dataSource type=_"POOLED"_\>

\<property name=_"driver"_ value=_"\${jdbc.driver}"_/\>

\<property name=_"url"_ value=_"\${jdbc.url}"_/\>

\<property name=_"username"_ value=_"\${jdbc.username}"_/\>

\<property name=_"password"_ value=_"\${jdbc.password}"_/\>

\</dataSource\>

\</environment\>

\</environments\>

\<!-- 配置映射信息 --\>

\<mappers\>

\<!-- 配置 dao 接口的位置，它有两种方式

第一种：使用 mapper 标签配置 class 属性

第二种：使用 package 标签，直接指定 dao 接口所在的包

\--\>

\<package name=_"com.xqc.dao"_/\>

\</mappers\>

\</configuration\>

编写测试方法

/\*\*

\*

\* \<p\>Title: MybatisAnnotationCRUDTest\</p\>

\* \<p\>Description: mybatis 的注解 crud 测试\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public class** MybatisAnnotationCRUDTest {

/\*\*

\* 测试查询所有

\*/

\@Test

**public void** testFindAll() {

List\<User\> users = userDao.findAll();

**for**(User user : users) {

System.**out**.println(user);

}

}

/\*\*

\* 测试查询一个

\*/

\@Test

**public void** testFindById() {

User user = userDao.findById(41);

System.**out**.println(user);

}

/\*\*

\* 测试保存

\*/

\@Test

**public void** testSave() {

User user = **new** User();

user.setUserName("mybatis annotation");

user.setUserSex("男");

user.setUserAddress("北京市顺义区");

user.setUserBirthday(**new** Date());

**int** res = userDao.saveUser(user);

System.**out**.println("影响数据库记录的行数："+res);

System.**out**.println("插入的主键值："+user.getUserId());

}

/\*\*

\* 测试更新

\*/

\@Test

**public void** testUpdate() {

User user = userDao.findById(63);

user.setUserBirthday(**new** Date());

user.setUserSex("女");

**int** res = userDao.updateUser(user);

System.**out**.println(res);

}

/\*\*

\* 测试删除

\*/

\@Test

**public void** testDelete() {

**int** res = userDao.deleteUser(63);

System.**out**.println(res);

}

/\*\*

\* 测试查询使用聚合函数

\*/

\@Test

**public void** testFindTotal() {

**int** res = userDao.findTotal();

System.**out**.println(res);

}

/\*\*

\* 测试模糊查询

\*/

\@Test

**public void** testFindByName() {

List\<User\> users = userDao.findByName("%m%");

**for**(User user : users) {

System.**out**.println(user);

}

}

**private** InputStream in;

**private** SqlSessionFactory factory;

**private** SqlSession session;

**private** IUserDao userDao;

\@Before//junit 的注解

**public void** init()**throws** Exception{

//1.读取配置文件

in = Resources._getResourceAsStream_("SqlMapConfig.xml");

//2.创建工厂

SqlSessionFactoryBuilder builder = **new** SqlSessionFactoryBuilder();

factory = builder.build(in);

//3.创建 session

session = factory.openSession();

//4.创建代理对象

userDao = session.getMapper(IUserDao.**class**);

}

\@After//junit 的注解

**public void** destroy()**throws** Exception {

//提交事务

session.commit();

//释放资源

session.close();

//关闭流

in.close();

}

}

## **3 ：**使用注解实现复杂关系映射开发

实现复杂关系映射之前我们可以在映射文件中通过配置\<resultMap\>来实现，在使用注解开发时我们需要借助\@Results 注解，\@Result 注解，\@One 注解，\@Many 注解。

### 3.1 复杂关系映射的注解说明

**\@Results**注解

代替的是标签**\<resultMap\>**

该注解中可以使用单个\@Result 注解，也可以使用\@Result 集合

\@Results（{\@Result（），\@Result（）}）或\@Results（\@Result（））

\@Resutl 注解

代替了 **\<id\>**标签和**\<result\>**标签

**\@Result** 中 属性介绍：

id 是否是主键字段

column 数据库的列名

property 需要装配的属性名

one 需要使用的\@One 注解（\@Result（one=\@One）（）））

many 需要使用的\@Many 注解（\@Result（many=\@many）（）））

**\@One**注解（一对一）

代替了**\<assocation\>**标签，是多表查询的关键，在注解中用来指定子查询返回单一对象。

**\@One**注解属性介绍：

**select** 指定用来多表查询的**sqlmapper**

fetchType 会覆盖全局的配置参数 lazyLoadingEnabled。。

使用格式：

\@Result(column=" ",property="",one=\@One(select=""))

**\@Many**注解（多对一）

代替了**\<**Collection**\>**标签**,**是是多表查询的关键，在注解中用来指定子查询返回对象集合。

注意：聚集元素用来处理“一对多”的关系。需要指定映射的 Java 实体类的属性，属性的 javaType（一般为 ArrayList）但是注解中可以不定义；

使用格式：

\@Result(property="",column="",many=\@Many(select=""))

### 3.2 一对一复杂关系映射及延迟加载

需求：

加载账户信息时并且加载该账户的用户信息，根据情况可实现延迟加载。（注解方式实现）

添加**User**实体类及**Account**实体类

/\*\*

\*

\* \<p\>Title: User\</p\>

\* \<p\>Description: 用户的实体类\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public class** User **implements** Serializable {

**private** Integer userId;

**private** String userName;

**private** Date userBirthday;

**private** String userSex;

**private** String userAddress;

**public** Integer getUserId() {

**return** userId;

}

\@Override

**public** String toString() {

**return** "User [userId=" + userId + ", userName=" + userName + ",
userBirthday=" + userBirthday + ", userSex="

\+ userSex + ", userAddress=" + userAddress + "]";

}

}

/\*\*

\*

\* \<p\>Title: Account\</p\>

\* \<p\>Description: 账户的实体类\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public class** Account **implements** Serializable {

**private** Integer id;

**private** Integer uid;

**private** Double money;

//多对一关系映射：从表方应该包含一个主表方的对象引用

**private** User user;

\@Override

**public** String toString() {

**return** "Account [id=" + id + ", uid=" + uid + ", money=" + money + "]";

}

}

添加账户的持久层接口并使用注解配置

/\*\*

\*

\* \<p\>Title: IAccountDao\</p\>

\* \<p\>Description: 账户的持久层接口\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public interface** IAccountDao {

/\*\*

\* 查询所有账户，采用延迟加载的方式查询账户的所属用户

\* **\@return**

\*/

\@Select("select \* from account")

\@Results(id="accountMap",

value= {

\@Result(id=**true**,column="id",property="id"),

\@Result(column="uid",property="uid"),

\@Result(column="money",property="money"),

\@Result(column="uid", property="user",

one=\@One(select="com.xqc.dao.IUserDao.findById",

fetchType=FetchType.**LAZY**)

)

})

List\<Account\> findAll();

}

添加用户的持久层接口并使用注解配置

/\*\*

\*

\* \<p\>Title: IUserDao\</p\>

\* \<p\>Description: 用户的持久层接口\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public interface** IUserDao {

/\*\*

\* 查询所有用户

\* **\@return**

\*/

\@Select("select \* from user")

\@Results(id="userMap",

value= {

\@Result(id=**true**,column="id",property="userId"),

\@Result(column="username",property="userName"),

\@Result(column="sex",property="userSex"),

\@Result(column="address",property="userAddress"),

\@Result(column="birthday",property="userBirthday")

})

List\<User\> findAll();

\@select（“select \* from user where id = \#{uid}”）

\@ResultMap(“userMap”)

User findById(Integer userId);

**测试一对一关联及延迟加载**

**3.3.2.4** 测试一对一关联及延迟加载

/\*\*

\*

\* \<p\>Title: AccountTest\</p\>

\* \<p\>Description: 账户的测试类\</p\>

\* \<p\>Company: http://www.xqc.com/ \</p\>

\*/

**public class** AccountTest {

\@Test

**public void** testFindAll() {

List\<Account\> accounts = accountDao.findAll();

// for(Account account : accounts) {

// System.out.println(account);

// System.out.println(account.getUser());

// }

}

## **4 mybatis**基于注解的二级缓存

### 1 在 SqlMapConfig 中开启二级缓存支持

```xml
<!-- 配置二级缓存 -->

<settings>

<!-- 开启二级缓存的支持 -->

<setting name=*"cacheEnabled"* value=*"true"*>

</setting>

```

在持久层接口中使用注解配置二级缓存

```java
@CacheNamespace(blocking=true)
//mybatis基于注解方式实现配置二级缓存
Public interface IuserDao {}
```

sql 注入

# 第十三章：流式查询

流式查询指的是查询成功后不是返回一个集合而是返回一个迭代器，应用每次从迭代器取一条查询结果。流式查询的好处是能够降低内存使用。

如果没有流式查询，我们想要从数据库取 1000 万条记录而又没有足够的内存时，就不得不分页查询，而分页查询效率取决于表设计，如果设计的不好，就无法执行高效的分页查询。因此流式查询是一个数据库访问框架必须具备的功能。

## 流式查询接口

Cursor 的接口类

1. Cursor 是可关闭的；
2. Cursor 是可遍历的。

Cursor 还提供了三个方法：

1. isOpen()：用于在取数据之前判断 Cursor 对象是否是打开状态。只有当打开时 Cursor 才能取数据；
2. isConsumed()：用于判断查询结果是否全部取完。
3. getCurrentIndex()：返回已经获取了多少条数据

因为 Cursor 实现了迭代器接口，因此在实际使用当中，从 Cursor 取数据非常简单：

```java
cursor.forEach(rowObject -> {...});
```

## 构建 Cursor

我们举个实际例子。下面是一个 Mapper 类：

```java
@Mapper
public interface FooMapper {
	@Select("select * from foo limit #{limit}")
	Cursor<Foo> scan(@Param("limit") int limit);
}
```

方法 scan() 是一个非常简单的查询。通过指定 Mapper 方法的返回值为 Cursor 类型，MyBatis 就知道这个查询方法一个流式查询。

然后我们再写一个 SpringMVC Controller 方法来调用 Mapper（无关的代码已经省略）：

```java
@GetMapping("foo/scan/0/{limit}")
public void scanFoo0(@PathVariable("limit") int limit) throws Exception {
	try (Cursor<Foo> cursor = fooMapper.scan(limit)) {  // 1
	cursor.forEach(foo -> {});                      // 2
	}
}
```

上面的代码中，fooMapper 是 @Autowired 进来的。

注释

1 处调用 scan 方法，得到 Cursor 对象并保证它能最后关闭；

2 处则是从 cursor 中取数据。

上面的代码看上去没什么问题，但是执行 scanFoo0() 时会报错：

```
java.lang.IllegalStateException: A Cursor is already closed.
```

这是因为我们前面说了在取数据的过程中需要保持数据库连接，而 Mapper 方法通常在执行完后连接就关闭了，因此 Cusor 也一并关闭了。

所以，解决这个问题的思路不复杂，保持数据库连接打开即可。我们至少有三种方案可选。

**方案一：SqlSessionFactory**

我们可以用 SqlSessionFactory 来手工打开数据库连接，将 Controller 方法修改如下：

```java
@GetMapping("foo/scan/1/{limit}")
public void scanFoo1(@PathVariable("limit") int limit) throws Exception {
	try (
		SqlSession sqlSession = sqlSessionFactory.openSession();  // 1
		Cursor<Foo> cursor =  sqlSession.getMapper(FooMapper.class).scan(limit)   // 2
	) {
		cursor.forEach(foo -> { });
	}
}
```

上面的代码中，1 处我们开启了一个 SqlSession （实际上也代表了一个数据库连接），并保证它最后能关闭；2 处我们使用 SqlSession 来获得 Mapper 对象。这样才能保证得到的 Cursor 对象是打开状态的。

**方案二：TransactionTemplate**

在 Spring 中，我们可以用 TransactionTemplate 来执行一个数据库事务，这个过程中数据库连接同样是打开的。代码如下：

```java
@GetMapping("foo/scan/2/{limit}")
public void scanFoo2(@PathVariable("limit") int limit) throws Exception {
	TransactionTemplate transactionTemplate = new TransactionTemplate(transactionManager);  // 1
    transactionTemplate.execute(status -> {               // 2
    	try (Cursor<Foo> cursor = fooMapper.scan(limit)) {
    		cursor.forEach(foo -> { });
    	} catch (IOException e) {
    		e.printStackTrace();
    	}
    	return null;
    });
}
```

上面的代码中

1 处我们创建了一个 TransactionTemplate 对象（此处 transactionManager 是怎么来的不用多解释，本文假设读者对 Spring 数据库事务的使用比较熟悉了）

2 处执行数据库事务，而数据库事务的内容则是调用 Mapper 对象的流式查询。注意这里的 Mapper 对象无需通过 SqlSession 创建。

**方案三：@Transactional 注解**

这个本质上和方案二一样，代码如下：

```java
@GetMapping("foo/scan/3/{limit}")
@Transactional
public void scanFoo3(@PathVariable("limit") int limit) throws Exception {
	try (Cursor<Foo> cursor = fooMapper.scan(limit)) {
		cursor.forEach(foo -> { });
	}
}
```

仅仅是在原来方法上面加了个 @Transactional 注解。这个方案看上去最简洁，但请注意 Spring 框架当中注解使用的坑：只在外部调用时生效。在当前类中调用这个方法，依旧会报错

以上是三种实现 MyBatis 流式查询的方法。

# 第十四章：原理

简述 Mybatis 的插件运行原理，以及如何编写一个插件。

答：Mybatis 仅可以编写针对 ParameterHandler、ResultSetHandler、StatementHandler、Executor 这 4 种接口的插件，Mybatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的 invoke 方法，当然，只会拦截那些你指定需要拦截的方法。

编写插件：实现 Mybatis 的 Interceptor 接口并复写 intercept 方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件
