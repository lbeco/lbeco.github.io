# Java杂项

## switch

不管是char,short,byte类型都是转化为int之后走的判断。
而long ,float,double转换为int都会丢失进度，boolean无法转换为int,
所以能用在switch上的类型有，char ,byte,short,int以及相应的包装类型。
1.7支持string的switch判断 把hash值提取出来算

类变量 成员变量 局部变量

## fail fast 和 fail-safe

 https://hollischuang.github.io/toBeTopJavaer/#/basics/java-basic/fail-fast-vs-fail-safe

Java的集合类中运用了fail-fast机制进行设计。如果在foreach 循环里对某些集合元素进行元素的 remove/add 操作的时候，就会触发fail-fast机制，进而抛出CMException。

CopyOnWriteArrayList等集合类实现了fail-safe

## 泛型

<https://blog.csdn.net/u014674862/article/details/105676880>

Java泛型（ generics） 是JDK 5中引⼊的⼀个新特性， 允许在定义类和接口的时候使⽤类型参数（ type parameter） 。

Java语言的泛型采用的是擦除法实现的伪泛型，泛型信息（类型变量、参数化类型）编译之后通通被除掉了。使用擦除法的好处就是实现简单、非常容易Backsupport，运行期也能够节省一些类型所占的内存空间。而擦除法的坏处就是，通过这种机制实现的泛型远不如真泛型灵活和强大。Java选取这种方法是一种折中，因为Java最开始的版本是不支持泛型的，为了兼容以前的库而不得不使用擦除法。

泛型类型只有在静态类型检查期间才出现，在此之后，程序中的所有泛型类型都将被擦除，替换成它们非泛型上界。

new ArrayList<String>().getClass(); -> new ArrayList<>();

## SPI



### 步骤

- 在jar包（服务提供者）的META-INF/services/目录中，新建一个文件，文件名为SPI接口的"全限定名"。 文件内容为该接口的具体实现类的"全限定名"

- 将spi所在jar放在主程序的classpath中

- 服务调用方使用java.util.ServiceLoader去动态加载具体的实现类到JVM中
- 

### 应用案例

#### 数据库接口

java.sql.Driver的spi实现，有mysql驱动、oracle驱动等。以mysql为例，实现类是com.mysql.jdbc.Driver，在mysql-connector-java-5.1.6.jar中，我们可以看到有一个META-INF/services目录，目录下有一个文件名为java.sql.Driver的文件，其中的内容是com.mysql.jdbc.Driver。

不同的数据库实现相同的接口，然后通过SPI技术加载。

#### 日志

slf4j是一个典型的门面接口，早起我们使用log4j作为日记记录框架，我们需要同时引入slf4j和log4j的依赖。后面比较流行logback，我们也想要把项目切换到logback上来，此时利用SPI的机制，我们只需要把log4j的jar包替换为logback的jar包就可以了。


## SOLID

SOLID原则并非单纯的1个原则,而是由5个设计原则组成

**单一职责原则 **一个类只代表一种对象定义，只做一种类型责任。

**开闭原则** 通过增加代码来实现功能，而不是修改。

**里氏替换原则** 父类都可以用子类实现

**接口隔离原则** 把单个接口分开，客户可以按需继承单一功能接口子类

**依赖反转原则** 抽象不应该依赖于细节，细节应该依赖于抽象。

## String

https://zhuanlan.zhihu.com/p/458378633

https://www.freesion.com/article/2419138106/

下面的对任何版本都适用。

```java
String a = "123";
String b = "123";
System.out.println(a == b);  //true
 
 
String c = new String("123");
String d = new String("123");
System.out.println(c == d);  //false
```

JDK6，字符串常量池实现在永久代

JDK7，字符串常量池实现在堆中

**intern()方法：**

1.6： 当字符串在常量池存在时，则返回常量池中的字符串；当字符串在常量池不存在时，则在常量池中拷贝一份，然后再返回常量池中的字符串。

所以：

```java
String a = new String("1")+new String("2");
String b = a.intern();
System.out.println(a == b);  //false
```

a直接指向常量池中拷贝的字符串，b指向堆里的对象。

1.7

当字符串在常量池存在时，则返回常量池中的字符串；当字符串在常量池不存在时，则把堆内存中此对象引用添加到常量池中，然后再返回此引用。此时：

```java
String a = new String("1")+new String("2");
String b = a.intern();
System.out.println(a == b);  //true
```

a和b一样都是指向堆内对象的指针，所以a==b。这样做减少内存的占用，提高运行时的性能。

