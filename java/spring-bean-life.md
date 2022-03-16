---
typora-copy-images-to: ..\map
typora-root-url: ..\map
---



# Spring Bean生命周期——张三的一生



本文参考了<https://www.cnblogs.com/zrtqsk/p/3735273.html>里的内容，或者说就是把他详细地实现了一下。换句话说，本文就是跑了一遍别人的代码，然后解释了一通，相当缺乏技术含量，特此说明。

Spring Bean生命周期的研究：

面试被问到了spring生命周期，然后果不其然地啥也不会。这里做一个简单的研究。首先是总的流程图：

![img](/181453414212066-16364637643662.png)

![img](/181454040628981.png)

好了，就把这个一字不差地背下来，相信面试官都会为你的牛逼鼓掌，本篇文章到此结束。





那当然不能这么搞，这里我还是要从头到尾梳理一遍整个的流程。原程序是原生的spring，虽然现在我们都是springboot一把梭不这么倒腾就是了。

我们先把程序都给扒拉进来，再略加修改，可以得到：

>Connected to the target VM, address: '127.0.0.1:53674', transport: 'socket'
>**现在开始初始化容器**
>19:41:26.743 [main] DEBUG org.springframework.context.support.ClassPathXmlApplicationContext - Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@2437c6dc
>19:41:26.936 [main] DEBUG org.springframework.beans.factory.xml.XmlBeanDefinitionReader - Loaded 3 bean definitions from class path resource [beans.xml]
>19:41:26.980 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'beanFactoryPostProcessor'
>**这是BeanFactoryPostProcessor实现类构造器！！**
>**BeanFactoryPostProcessor调用postProcessBeanFactory方法**
>19:41:28.606 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'beanPostProcessor'
>**这是BeanPostProcessor实现类构造器！！**
>19:41:28.615 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'springLifeTest'
>**【构造器】调用Person的构造器实例化**
>**【注入属性】注入属性address**
>**【注入属性】注入属性name**
>**【注入属性】注入属性phone**
>**【BeanNameAware接口】调用BeanNameAware.setBeanName()**
>**【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()**
>**BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！**
>**【InitializingBean接口】调用InitializingBean.afterPropertiesSet()**
>**【init-method】调用<bean>的init-method属性指定的初始化方法**
>**BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！**
>**容器初始化成功**
>**Person [address=广州, name=张三, phone=120]**
>**现在开始关闭容器！**
>19:41:30.248 [SpringContextShutdownHook] DEBUG org.springframework.context.support.ClassPathXmlApplicationContext - Closing org.springframework.context.support.ClassPathXmlApplicationContext@2437c6dc, started on Tue Nov 09 19:41:26 CST 2021
>**【DiposibleBean接口】调用DiposibleBean.destory()**
>**【destroy-method】调用<bean>的destroy-method属性指定的初始化方法**

我相信看了这么多，大家应该还是一头雾水。这都干啥去了一个个。 我们从头捋一遍：

我们假设张三是一个什么生化人，哆啦A人或者是尤里复制人。总而言之，张三不是娘胎里生的，生产张三的是一个工厂。在张三来到这个世界上之前，首先是全知全能的上帝（Spring）出现创造了世界，然后下一步是生产张三的工厂先开张。而我们的工厂就是BeanFactory。

我们的超级工厂（BeanFactory）,他要来创造世界。他打开resource文件夹一看，首先就看到了beans.xml文件里面的图纸：

> ```java
> <bean id="beanPostProcessor" class="com.example.demo.config.MyBeanPostProcessor"></bean>
> 
> <bean id="beanFactoryPostProcessor" class="com.example.demo.config.MyBeanFactoryPostProcessor"></bean>
> 
> <bean id="person" class="com.example.demo.service.Person" init-method="myInit"
>           destroy-method="myDestory" scope="singleton" p:name="张三" p:address="广州"
>           p:phone="15900000000" />
> ```



第一张图纸是代替原装beanPostProcessor的MyBeanPostProcessor，第二张图纸是代替原装beanPostProcessor的MyBeanPostProcessor，第三张图纸就是我们可怜的张三。

BeanFactory首先创造了beanFactoryPostProcessor，但是事实上这时候我们还没创造beanPostProcessor和张三。我们这时候虽然还没创造张三，但是在beanFactoryPostProcessor里可以获得和修改修改张三的图纸（BeanDefinition）：

> ```java
> @Override
> public void postProcessBeanFactory(ConfigurableListableBeanFactory arg0)
>         throws BeansException {
>     System.out
>             .println("BeanFactoryPostProcessor调用postProcessBeanFactory方法");
>     BeanDefinition bd = arg0.getBeanDefinition("person");
>     bd.getPropertyValues().addPropertyValue("phone", "120");
> }
> ```

注意，这时候beanPostProcessor还没被生产出来。

接下来就是创造beanPostProcessor。我们的spring有一些默认的beanPostProcessor：

>beanPostProcessors = {AbstractBeanFactory$BeanPostProcessorCacheAwareList@1844}  size = 4
> 0 = {ApplicationContextAwareProcessor@1867} 
> 1 = {PostProcessorRegistrationDelegate$BeanPostProcessorChecker@2111} 
> 2 = {MyBeanPostProcessor@2112} 
> 3 = {ApplicationListenerDetector@2113} 

其中就包括了我们的MyBeanPostProcessor。其他的BeanPostProcessor有机会再介绍；

接下来就是创造张三，创造张三的时候前两个PostProcessor就已经出现在世界上了。BeanFactory拿出张三的图纸（BeanDefinition），开始走创造张三的流程。首先，BeanFactory实例化张三，接着把地址什么的注入给张三。

干完了这些，按道理我们应该松了一口气。但是呱呱坠地的张三来到这个世界上，有很多的疑问。比如他叫什么，他是哪里出来的。由于张三寡廉鲜耻地一口气实现了BeanFactoryAware, BeanNameAware, InitializingBean, DisposableBean这些接口，工厂在创建他的时候还得做许多的多余操作。

> ```java
> public class Person implements BeanFactoryAware, BeanNameAware,
>         InitializingBean, DisposableBean {
> ```

首先，张三想知道自己真名叫什么（bean的名称），于是调用BeanNameAware.setBeanName()。这时候张三知道原来自己在上帝（spring）眼中不是张三，只是普普通通的一个person

张三还想知道创造自己的工厂是哪个，于是张三调用BeanFactoryAware.setBeanFactory()。这时候张三又知道了创造自己的原来是一个工厂。

知道了原来自己就叫person，连个正经名字都没有，还是从工厂里蹦出来的这些残酷真相的张三，还没来得及结束自己的初始化流程，BeanPostProcessor突然出现，横插了一杠：快，让我康康！BeanPostProcessor在init方法前生效，可以对已经实例化的张三进行操作：

> ```java
> @Override
> public Object postProcessBeforeInitialization(Object arg0, String arg1)
>         throws BeansException {
>     System.out
>             .println("BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！");
>     ((Person)arg0).setPhone(12345); //电话设置为12345
>     return arg0;
> }
> ```

又过了一道鬼门关，张三终于可以调用afterPropertiesSet和图纸上提供给他的init-method方法了。这两个地方我们就不折腾张三了，要折腾的话就和BeanPostProcessor一样

> ```java
> // 这是InitializingBean接口方法
> @Override
> public void afterPropertiesSet() throws Exception {
>     System.out
>         .println("【InitializingBean接口】调用InitializingBean.afterPropertiesSet()");
> }
> // 通过<bean>的init-method属性指定的初始化方法
> public void myInit() {
>     System.out.println("【init-method】调用<bean>的init-method属性指定的初始化方法");
> }
> ```

终于完成了这一切，BeanPostProcessor来杀了个回马枪：再让我康康！

> ```java
> @Override
> public Object postProcessAfterInitialization(Object arg0, String arg1)
>         throws BeansException {
>     System.out
>             .println("BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！");
> 
>     return arg0;
> }
> ```

惨，不说了，张三的初始化就这么结束了，张三作为一个bean的人生正式开始

张三在世界上度过了不知道如何的一生，幸运的是由于我们都是默认配置，张三寿与天齐，容器关闭的时候张三才开始被删除（收尸）。由于张三实现了DisposableBean接口，他可以死前做点事情：

>```java
>// 这是DiposibleBean接口方法
>@Override
>public void destroy() throws Exception {
>    System.out.println("【DiposibleBean接口】调用DiposibleBean.destory()");
>}
>```

张三还可以指定一个收尸的方法。这个在张三的图纸中提到过，在这时起了作用：

```java
    // 通过<bean>的destroy-method属性指定的初始化方法
    public void myDestory() {
        System.out.println("【destroy-method】调用<bean>的destroy-method属性指定的初始化方法");
    }
```



就这样，张三波澜壮阔的一生草草结束了。看到这些，相信大家对bean的生命周期有了很深的理解。我们下期见





* 注：原文https://www.cnblogs.com/zrtqsk/p/3735273.html中的InstantiationAwareBeanPostProcessorAdapter方法在5.3后被弃用，官方推荐直接继承BeanPostProcessor来进行操作。姑不考



