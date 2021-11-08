Java泛型（ generics） 是JDK 5中引⼊的⼀个新特性， 允许在定义类和接⼜的时候使⽤类型参数（ type parameter） 。

Java语言的泛型采用的是擦除法实现的伪泛型，泛型信息（类型变量、参数化类型）编译之后通通被除掉了。使用擦除法的好处就是实现简单、非常容易Backsupport，运行期也能够节省一些类型所占的内存空间。而擦除法的坏处就是，通过这种机制实现的泛型远不如真泛型灵活和强大。Java选取这种方法是一种折中，因为Java最开始的版本是不支持泛型的，为了兼容以前的库而不得不使用擦除法。

泛型类型只有在静态类型检查期间才出现，在此之后，程序中的所有泛型类型都将被擦除，替换成它们非泛型上界。

new ArrayList<String>().getClass(); -> new ArrayList<>();

new ArrayList<String>().getClass(); -> new ArrayList<>();