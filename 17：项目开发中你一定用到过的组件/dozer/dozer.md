复制对象，有点像beanUtil，根据配置完成两个对象的复制；

使 用：

```java
UserEntity user = mapper.map(UserDTO,UserEntity.class);
```

两个对象如果名称或者类型相同不用配置，但是如果不同要配置

