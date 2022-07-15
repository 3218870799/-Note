mybatis一级缓存；

前几天查问题，也是一个非常常见的一个问题；

大体上进行了这样一种操作，但是是在不同方法里的，就显得很隐蔽了；

```java
//原数据   张三  18

UserQuery query = new UserQuery();
query.setUserName("张三");
User a = UserMapper.getUser(query);
a.setAge(60);

//另一个方法中，参数没改
query.setUserName("张三");
User b = UserMapper.getUser(query);
int age = b.getAge();
//此时age = 60
```

这个时候由于一级缓存的存在，参数都没改，所以就直接将a给了b，但是这个时候a是已经改变了的，所以就错了；

有两种解决办法，一种是每一次都去查，不使用缓存，一种是不改变查出来的对象；

方式一：不使用缓存；

1：直接禁用缓存

```xml
<select id="getUser" flushCache="true">
</select>
```

2：使用唯一的标识

```sql
select * from user where userName = "张三" and NOW() = NOW()
```

方式二：

复制1一个对象取更改，原来的对象不更改，注意一定是深拷贝；

