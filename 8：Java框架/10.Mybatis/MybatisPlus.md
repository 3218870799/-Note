# 第一章：概述

官网：https://mp.baomidou.com/guide/

无侵入，不需要修改现在的代码；内置生成工具；内置分页插件；

## 快速开始

https://mp.baomidou.com/guide/quick-start.html

添加依赖；匹配值；编码；

真实开发中，version(乐观锁)，deleted（逻辑删除），创建时间，更新时间等其他都是必须有的（阿里巴巴开发规范）









## 通用 CRUD

基于 Mybatis：

编写 EmployeeMapper 接口，并手动编写 CRUD 方法，提供 EmployeeMapper.xml 映射文件，并收订编写每个方法的 SQL 语句。

基于 MP：

只需要创建 EmployeeMapper 接口，并继承 BaseMapper 接口，就是使用 MP 需要完成的所有操作，甚至不需要创建 SQL 映射文件。

BaseMapper 接口中有很多通用接口。

2：主键策略：

有四种主键策略：

| 值                | 描述                                      | 备注 |
| ----------------- | ----------------------------------------- | ---- |
| IdType.AUTO       | 数据库 ID 自增                            |      |
| IdType.INPUT      | 用户输入 ID                               |      |
| IdType.ID_WORKEER | 全局唯一 ID，内容为空自动填充（默认配置） |      |
| IdType.UUID       | 全局唯一 ID，内容为空自动填充             |      |





在实体类上标注对应表，

@TableId：

value ：指定表中的主键列的列名，如果实体属性与列名一直，可以省略不指定。

type：指定主键策略

@TableName：

默认使用实体类的类名去找表，一般都不一样，

value=“t_employee”

mybatisPlus 会自动驼峰和下划线匹配，比如实体为 lastName ，表的字段为 last_name，也可以匹配上。

mybatis2.3 默认配置了全局策略配置中的驼峰为 true

```xml
<property name="dbColumnUnderline" value="true"></property>
```

全局配置

注意，下边的配置还是要引入到 Mybatis 的配置中

```xml
<bean id = "globalConfiguration" class="com.baomidou.mybatisplus.entity.GlobalConfiguration">
    <!--支持驼峰下划线匹配-->
    <property name="dbColumnUnderline" value="true"></property>
    <!--配置全局的主键策略-->
    <property name="idType" value="0"></property>
    <!--全局的表前缀策略配置-->
    <property name = "tablePrefix" value =" t_"></property>
</bean>
```

查询操作：

```java
// 根据 ID 查询
T selectById(Serializable id);
// 根据 entity 条件，查询一条记录
T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 查询（根据ID 批量查询）
List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);
// 根据 entity 条件，查询全部记录
List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 查询（根据 columnMap 条件）
List<T> selectByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);
// 根据 Wrapper 条件，查询全部记录
List<Map<String, Object>> selectMaps(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询全部记录。注意： 只返回第一个字段的值
List<Object> selectObjs(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据 entity 条件，查询全部记录（并翻页）
IPage<T> selectPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询全部记录（并翻页）
IPage<Map<String, Object>> selectMapsPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询总记录数
Integer selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

RowBounds：

直接传递 MybatisPlus 里的 Page 对象，分页使用的还是 RowBounds 使用的内存分页，如果想使用物理分页，还是需要第三方的分页。

条件构造器

EntityWrapper，实体包装器。主要用于处理 sql 拼接，排序，实体参数查询等。注意使用的是数据库字段

Condition：

例：分页查询 t_employee 表中，年龄在 18-50 之间性别为男且姓名为 XX 的所有用户

```java
employeeMapper.selectPage(new Page<Emplpyee>(1,2),
                         new EntityWrapper<Employee>()
                          .between("age",18,50)
                         .eq("gender",1)
                         .eq("last_name","Tom")
                        );
```

.or()方法后边跟或的插叙条件：SQL： gender = ？ AND last_name like ? OR email LIKE ?

.ornew()方法后边跟或的查询条件：SQL：（gender = ？ AND last_name like ? ) OR (email LIKE ?)

活动记录 ActiveRecord

实体类继承 Model<>类，实现未实现的方法。

使用实体类进行操作，方法很多和 Base 一样。



## 乐观锁



## 分页插件



## 性能分析

