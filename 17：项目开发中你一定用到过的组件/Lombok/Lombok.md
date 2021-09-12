## 注解

1：@Getting和@Setting

可以用在类上和属性上，生成get和set方法和一个无参构造器；

2：@ToString：

3：@EqualsAndHahCode：同时生成equals和HashCode

4：@AllArgsConstructor：注解再类上，提供全参数的构造方法。默认不提供无参构造；

5：@NoArgsContructor：无参构造器

6：@Data：自动生成，上边的几个一起；

7：@Slf4j：在类上注解后，可直接调用log

```java
log.info(xxx);
```

8：@SneakyThrows：抛出异常，不用再在方法名后面写throw

```java
@SneakyThrows(Exception.class)
```

9：@Synchronized：方法中所有代码都加入到一个代码块中，默认静态方法使用的是全局锁，普通方法使用的是对象锁，当然也可以指定锁得到对象；

```java
private final Object lock = new Object();
@Synchronized("lock")
public void foo(){
    //Do something
}
```



