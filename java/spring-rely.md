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