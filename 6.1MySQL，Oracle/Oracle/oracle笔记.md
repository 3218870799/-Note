# 一：Oracle的基本概念与安装

## 1：简介

ORACLE数据库系统是美国ORACLE公司（甲骨文）提供的以分布式数据库为核心的一组软件产品，是目前最流行B/S体系结构的数据库之一。ORACLE数据库是目前世界上使用最为广泛的数据库管理系统，作为一个通用的数据库系统，它具有完整的数据管理功能；作为一个关系数据库，它是一个完备关系的产品；作为分布式数据库它实现了分布式处理功能。但它的所有知识，只要在一种机型上学习了ORACLE知识，便能在各种类型的机器上使用它。

## 2：服务端安装

>   1．解压。oracle数据库安装包，如果是win7或者win8系统右键点击setupexe选择兼容性，以XP方式，并且以管理员方式运行，以及其他所有用户都按着此规则如图

![](media/b0ac13c4f0e737e08ffff2cb343c2b7a.jpg)

![](media/565ce9a65d747cc41161a55bf65379aa.png)

![](media/8fe585afafaa998f8be5a85f1f9dd1dc.jpg)

![](media/551d9a425f2d7a06bf128f1ce4b0eab0.jpg)

## 3：自带客户端工具的安装

## 4：PLSQL工具的安装

（1）设置tools->preferences->connection中sqlplus路径

![IMG_256](media/clip_image001.jpg)

​      （2）确认服务器上的tnsnames.ora和linstener中的host为虚拟机ip地址

![img](media/clip_image003.jpg)

 

  　（3）将服务器中tnsnames.ora文件拷贝到本地路径无需修改，如果是拷贝发的资料需要修改文件中远程orcl服务器的ip地址

![img](media/clip_image005.jpg)

 　（4）系统变量中设置TNS_ADMIN变量为tnsnames.ora文件目录

![img](media/clip_image007.jpg)

 

 8.解决中文乱码问题

查询select userenv('language') from dual;--AMERICAN_AMERICA.ZHS16GBK

查询select * from V$NLS_PARAMETERS;

在系统变量中设置NLS_LANG=AMERICAN_AMERICA.ZHS16GBK

重新启动PLSQL

## 5：基本使用

解锁scott用户设置密码为tiger

OracleDBConsoleorcl --可以不启动,用于管理Oracle的企业管理器的服务.

OracleJobSchedulerORCL --通常不启动,用于定期操作任务的服务

OracleOraDb10g_home1iSQL*Plus --可以不启动,isqlplus服务,用网页执行sql,11g已经取消这个功能

OracleOraDb10g_home1TNSListener --必须启动,这是临听,用于远程客户端连接你的Oracle

OracleServiceORCL --必须启动,这是Oracle数据库的服务



# 二：Oracle体系结构

## 1：基本概念

![](media/90b7f1e02669710fa5476b800aeff8b2.emf)

实例

![](media/89f5426228a505f601e60857f5af9ce6.png)

![](media/08eff5dec827cc588a4656c088cdcb68.png)

![](media/66e22ed466ead06716fe6e1dd7f5163e.png)

![](media/50be7eec74bc43c5ed6f19f4750ff500.png)

## 2：使用DDL语句管理表

\--创建表空间

```sql
create tablespace xqc
--删除表空间
drop tablespace xqc
--创建用户
create user xqc
identified by xqc
default tablespace xqc
--给用户授权
--oracle中常见角色
connect --连接角色，基本角色
Resource--开发者角色
Dba——超级管理员角色
--给xqc授予dba角色
grant dba to xqc
--切换到xqc用户下
--修改列类型
alter table person modify gender char(1)
--char为固定长度
--修改列名称
alter table person drop colum sex;
序列的使用
--默认重1开始，依次递增，主要给主键赋值使用
create sequence s_person
dual:虚表，只为了补全语法，没有任何意义
select s_person..currval from dual;
Scott用户，密码为tiger
--解锁scott用户
alter user scott account unlock;
--解锁scoott用户的密码(也可以用来重置密码)
alter user scott identified by tiger
--切换到scoott用户，默认有四张表

```



# 三：基本语法

sql的基本语法参考mysql的，这里着重写oracle与mysql不同的地方





## 1：Select

## 2：过滤与排序

## 1：多表查询

### 1：外连接

## 2：子查询

## 3：集合运算

# 四：函数

## 1：通用函数（Oracle独有）

1.1：NVL

NVL(列|默认的代替值)

如果oracle的列为空那么显示第二个参数的值，如果列的值不为空，则显示列本来的值。

```sql
--算出工资+奖金的总和，但是奖金有空值，如果null和任意数字做算术运算，结果都是null
lect e.sal * 12 +e.comm from emp e;
select e.sal * 12 + nvl(e.comm,0) from emp e;
```

1.2：DECODE多数值判断

DECODE(列,匹配内容1，显示内容1，匹配内容2，显示内容2,未匹配内容的默认值);

根据不同的结果在输出的时候进行一个数据的转换



call函数



## 2：单行函数

单行函数：作用于一行，返回一个值

多行函数：作用于多行，返回一个值

是放在SQL中还是放在Java中处理？？

2.1：trunc直接截取小数

TRUNC(列|数字 [,保留小数位])

2.2：字符函数

```sql
--转大写
select upper('yes') from dual;--YES
--转小写
select lower('YES') from dual;--yes
length（），
replace（），
substr()


```



2.3：数值函数

```sql
--四舍五入，后边参数表示保留的位数
select round(26.16,1) from dual;
trunc（），
mod（）
```

2.4：日期函数（不同于mysql）

```sql
--查询emp表中所有员工入职距离现在几天
select sysdate - e.hiredate from emp e;
--算出明天此刻
select sysdate+1 from dual;
add_months(),

--查询距离现在几月
select months_between(sysdate,e.hiredate) from emp e;
--查询距离现在几年
select moths_between(sysdate,e.hiredate)/12 from emp e;
--查询距离现在几周
select round((sysdate-e.hiredate)/7) from emp e;
--转换函数
select to_char(sysdate,'fm yyyy-mm-dd hh24:mi:ss') from dual;
--字符串转日期
select to_date('2018-6-7 16:39:50','fm yyyy-mm-dd hh24:mi:ss') from dual;


```



转换函数

通用函数

nvl(),

nvl2()



## 2：多行函数

2.1：分页

```sql
--表工资倒序排序后，每页五条记录，查询第二页
select * from (
    select rownum rn ,e.* from(
        select * from emp order by sal desc
    )e where rownum<11
)where rn >5
```

Mybatis的PageHelper插件也是这个原理,在最外层过滤后添加语句

```sql
select * from(
	……
)where rownum <=20
```



![](media/1384040b47bbcb89c68b7904bdeea81e.png)

![](media/0e0ad2db4c21272ffb84e01944d71eea.png)

2.2：视图



![](media/ae6d2e9923ba9e28100a7a14ca58a32f.png)

![](media/097a50c6e45b25c339f9807585746d31.png)

![](media/4dce5a8c31c5e3036e978f072036298a.png)

索引

![](media/25fca7548d9f3f3f4092f47094dc27b6.png)

![](media/ccc53cdfc0be30e132d52a1d4b410646.png)

![](media/3ac62a8b77fd66d84822a93ef234b629.png)

# 五：事务

Oracle默认不自动提交，用户需手动提交，写commit或点击commit按钮，而mysql默认自动提交。



# 五：PLSQL的使用

程序结构，变量，流程控制，游标

指在SQL命令语言中增加了过程处理语句（如分支，循环等）

PLSQL新建TestWindow

PLSQL不区分大小写

## 5.1：程序结构

```plsql
declare
	--声明本地变量，游标
begin
	--执行语句
	--异常处理
end;
```

1：输出helloWorld

```plsql
declare
	--声明本地变量，游标
begin
	--java:System.out.println("Hello World");
	DBMS_OUTPUT.PUT_LINE('hello world');
end;
```

cmd输入sqlplus以scott登录密码为tiger

默认输出关闭，设置打开，以/结束



![image-20201209191753662](media/image-20201209191753662.png)

## 5.2：变量

变量名  变量类型（变量长度） 例如：v_name varchar2(20)

1）普通变量

直接赋值语句：=

语句赋值，使用select ……into……赋值

例：

```plsql
--打印人员信息
DECLARE
	--姓名
	V_NAME VARCHAR2(20) :='zhangsan';
	--薪水
	V_SAL NUMBER;
	--地址
	V_ADDR VARCHAR2(200);
BEGIN
	--直接赋值
	V_SAL:=1580;
	--语句赋值
	SELECT '上海' INTO V_ADDR FROM DUAL;
	--打印输出
	DBMS_OUTPUT.PUT_LINE('姓名：'||V_NAME||',薪水'||V_SAL||',地址：'||V_ADDR);
END;
```

2）引用型变量

```plsql
--查询emp表中员工信息
DECLARE
	--姓名，不再直接使用类型，而是根据emp表中字段的变化而变化
	V_NAME EMP.ENAME%TYPE :='zhangsan';
	--薪水,同理
	V_SAL EMP.SAL%TYPE;
BEGIN
	--查询表中的姓名和薪水并赋值给变量
	SELECT ENAME SAL INTO V_NAME,V_SAL FROM EMP WHERE EMPNO = 7839;
	--打印输出
	DBMS_OUTPUT.PUT_LINE('姓名：'||V_NAME||',薪水'||V_SAL);
END;
```

3）记录型变量

接收表中一整行记录，相当于Java中的一个对象

语法：变量名称 表名%ROWTYPE 例如V_EMP EMP%ROWTYPE

```PLSQL
--查询emp表中员工信息
DECLARE
	--记录型变量
	v_emp emp%ROWTYPE;
BEGIN
	--查询表中的姓名和薪水并赋值给变量
	SELECT * INTO EMP FROM EMP WHERE EMPNO = 7839;
	--打印输出
	DBMS_OUTPUT.PUT_LINE('姓名：'||EMP.ENAME||',薪水'||EMP.SAL);
END;
```

## 5.3：流程控制

1）条件分支

语法：

![image-20201209195633664](media/image-20201209195633664.png)

![image-20201209195841470](media/image-20201209195841470.png)

2）循环

语法：

![image-20201209200053002](media/image-20201209200053002.png)

例：

![image-20201209200031910](media/image-20201209200031910.png)

## 5.4：游标

用于存储一个查询返回的多行数据（结果集，类似于Java的jdbc连接返回的ResultSet集合）通过遍历游标，可以逐行访问处理该结果集的数据

使用

声明——>打开——>读取——>关闭

1）语法

![image-20201209202218846](media/image-20201209202218846.png)

2）游标的属性

![image-20201209202624158](media/image-20201209202624158.png)

3）例

![image-20201209202748063](media/image-20201209202748063.png)

![image-20201209202814661](media/image-20201209202814661.png)

4）带参数的游标



```plsql
--声明时修改
CURSOR c_emp(v_deptno emp.deptno%TYPE) IS ENAME,SAL FROM EMP WHERE deptno = v_deptno;
--打开时修改,传入参数
OPEN c_emp(10);
```



# 七：存储过程procedure

为了特定的业务功能，会向数据库进行多次连接关闭（连接关闭最耗费资源）需要对数据库进行多次IO读写，性能很低，

官方建议：能放在数据库中的就不要放到程序中，可能出错，而且数据库可以日志恢复

但是（维护可就难了）

语法

![image-20201209203912022](media/image-20201209203912022.png)

## 7.1：不带参数的

1：创建存储过程

```plsql
CREATE OR REPLACE PROCEDURE p_hello IS
BEGIN
	DBMS_OUTPUT.PUT_LINE('hello world');
END P_HELLO;
```

2：使用

```SQL
BEGIN 
	--直接输入调用存储过程的名称
	p_hello;
END P_HELLO;
```

在sqlplus中

![image-20201209204744272](media/image-20201209204744272.png)

注意：

is和as是可以互用的

过程中没有declare关键字，declare用在语句块中

## 7.2：带参数的存储过程

有输入参数和输出参数之分

![image-20201209205148883](media/image-20201209205148883.png)

使用

![image-20201209205255514](media/image-20201209205255514.png)



## 7.3：带输出参数的

![image-20201209205456875](media/image-20201209205456875.png)

![image-20201209205606226](media/image-20201209205606226.png)



## 7.4：Java执行存储过程

Connection对象

1）传统方式

```java
//1,加载驱动
Class.forName("oracle.jdbc.driver.OracleDriver");
//2，获取连接对象
String url = "jdbc:oracle:thin:@localhost:1521:xe";
String user = "scott";
String password = "tiger";

Connection conn = DriverManager.getConnection(url,user,password);
//3：获取语句对象
String sql = "{call p_querysal_out(?,?)}";
CallableStatement call = conn.prepareCall(sql);

//4:设置输出参数
call.setInt(1,7839);
//5:注册输出参数
call.registerOutParameter(2,OracleTypes.DOUBLE);
//6:执行存储过程
call.execute();
//7:获取输出参数
double sal =call.getDouble(2);

System.out.println(sal);
//8.释放资源
call.close();
conn.close();
```



![](media/a22e380e5fe4adc78ed468a783509077.png)

# 七：触发器





# 六：常用SQL语句



与mysql的区别

1：

mysql以表级锁为主，对资源锁定的粒度很大，虽然InnoDB引擎的表可以用行级锁，但这个行级锁的机制依赖于表的索引，如果表没有索引，或者sql语句没有使用索引，那么仍然使用表级锁。

oracle使用行级锁，对资源锁定的粒度要小很多，只是锁定sql需要的资源，并且加锁是在数据库中的数据行上，不依赖与索引。所以oracle对并发性的支持要好很多。



