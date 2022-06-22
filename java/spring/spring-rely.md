

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

- singletonObjects：**成品缓存**
- earlySingletonObjects： **半成品缓存**
- singletonFactories ：**单例工厂缓存**

为什么要二级缓存：

**解决循环依赖：**

- A引用创建后，提前暴露到半成品缓存中
- 依赖B，创建B ，B填充属性时发现依赖A， 先从成品缓存查找，没有,再从半成品缓存查找 取到A的早期引用。
- B顺利走完创建过程, 将B的早期引用从半成品缓存移动到成品缓存
- B创建完成，A获取到B的引用，继续创建。
- A创建完成，将A的早期引用从半成品缓存移动到成品缓存

为什么要三级缓存：因为有代理的存在。

**解决代理情况下的循环依赖：**

- A绑定到ObjectFactory 注册到工厂缓存singletonFactory中，
- B在填充A时，先查成品缓存有没有，再查半成品缓存有没有，最后看工厂缓存有没有单例工厂类，有A的ObjectFactory。调用getObject ，执行扩展逻辑，可能返回的代理引用，也可能返回原始引用。
- 成功获取到A的**早期引用**，早期引用可能会是直接进行代理的，尚未注入的引用。将A放入到半成品缓存中，B填充A引用完毕。
- 代理问题， 循环依赖问题都解决了。



为什么不直接统统生成早期引用：认为约定的是在initial方法前后生成aop，所以早期引用不讲武德，能不用就不用。

https://www.modb.pro/db/366156

 <https://zhuanlan.zhihu.com/p/382066829>

https://zhuanlan.zhihu.com/p/496273636

spring没法解决构造方法的循环依赖（废话，这无解的），遇到这种情况应当使用@Lazy



### 事务管理

声明式事务管理只需要用到**@Transactional** 注解和@EnableTransactionManagement。它是基于 **Spring AOP** 实现的，并且通过注解实现，实现起来简单，对原有代码没有入侵性。通过对业务类的加强，实现动态代理，从而实现事务功能

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

 