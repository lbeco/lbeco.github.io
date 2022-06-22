# Spring注解

## 常见注解

### @Component 

@Component指的是组件，

添加了@Component 注解，就会被注入ioc容器。



@Repository、@Service、@Controller

@Controller，@Repository和@Service 注解都被@Component修饰，用于代码中区分表现层，持久层和业务层的组件，代码中组件不好归类时可以使用@Component来标注

当前版本只有区分的作用，未来版本可能会添加更丰富的功能

### @Autowired

属性的依赖注入。

### @Resource

资源注入



