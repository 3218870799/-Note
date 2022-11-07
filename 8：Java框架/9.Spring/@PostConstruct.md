@PostConstruct注解用来修饰一个非静态的void方法。被@PostConstruct修饰的方法会在服务器加载servlet的时候执行；并且只会被执行一次，其执行在构造函数之后，init()方法之前；

在Spring中，bean的初始化中执行顺序为：构造方法——@Autowired——@PostConstruct



