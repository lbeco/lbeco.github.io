### 循环依赖

在DefaultSingletonBeanRegistry中有：

```java

	/** Cache of singleton objects: bean name to bean instance. */
	// 一级缓存
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

	/** Cache of singleton factories: bean name to ObjectFactory. */
	// 三级缓存
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

	/** Cache of early singleton objects: bean name to bean instance. */
	// 二级缓存
	private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);
```

如果有加@Autowired,就会搞出来一个叫做AbstractAutowireCapableBeanFactory来丢进三级缓存里面，这是为了解决循环依赖。





### 事务管理

声明式事务管理只需要用到@Transactional 注解和@EnableTransactionManagement。它是基于 **Spring AOP** 实现的，并且通过注解实现，实现起来简单，对原有代码没有入侵性。通过对业务类的加强，实现动态代理，从而实现事务功能

事务管理获取到RuntimeException或者Error后，抛出异常

spring将事务委托给不同平台的持久化机制框架来实现事务管理。由PlatformTransactionManager调用持久层的事务管理器进行commit和rollback



PROPAGATION_REQUIRED：当前没事务就开启，有就加入
PROPAGATION_SUPPORTS：当前有事务就加入，没有就不开事务
PROPAGATION_MANDATORY：当前有事务就加入，没有就报错
PROPAGATION_REQUIRES_NEW ：当前有事务就挂起该事务，先执行此事务后再执行之前的事务
PROPAGATION_NOT_SUPPORTED：当前有事务就挂起，反正自己不开事务
PROPAGATION_NEVER：当前有事务就报错，自己不会开事务
PROPAGATION_NESTED：当前有事务，通过savepoint产生嵌套事务



ISOLATION_DEFAULT

 使用底层数据库预设的隔离层级

ISOLATION_READ_COMMITTED

 允许事务读取其他并行的事务已经送出（Commit）的数据字段，可以防止Dirty read问题 

ISOLATION_READ_UNCOMMITTED

 允许事务读取其他并行的事务还没送出的数据，会发生Dirty、Nonrepeatable、Phantom read等问题

ISOLATION_REPEATABLE_READ

 要求多次读取的数据必须相同，除非事务本身更新数据，可防止Dirty、Nonrepeatable read问题

ISOLATION_SERIALIZABLE

 完整的隔离层级，可防止Dirty、Nonrepeatable、Phantom read等问题，会锁定对应的数据表格，因而有效率问题

 