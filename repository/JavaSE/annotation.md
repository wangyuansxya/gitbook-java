1，源码注解仅仅在源代码层面上有效

2，在被编译成字节码的时候，已经被完全擦除了

3，仅仅是起到标记作用，保证代码的健壮性

**一，注解的定义**

@Interface声明会创建一个Java接口，与其他接口一样，注解也将会编译成.class文件，注解的元素声明实际上是方法声明，注解的方法没有参数，没有throws语句，也不能使用泛型

**二，元注解**

顾名思义，元注解就是定义和实现注解的注解，共五种：

* @Target 取值是一个ElementType类型的数组，用来指定注解使用的范围，共10种不同的类型

| 元素类型 | 适用于
|---|---|
ANNOTATIONT_YPRE | 注解类型声明
CONSTRUCTOR |构造函数
FIELD |  实例变量
LOCAL_VARLABLE | 局部变量
METHOD | 方法
PACKAGE | 包
PARAMETER | 参数
TYPE | 类（包含enum）和接口（包含注解类型）
TYPE_PARAMETER | 类型参数
TYPE_USE | 类型用途

同时支持多种类型的注解定义如下

	@Target({ElementType.TYPE, ElementType.PACKAGE})
	public @interface Media{
	}
	
如果一个注解没有指定@Target类型，那么它可以用在除了TYPE_USE和TYPE_PARAMETER的其他类型中

* @Retention 用来指明注解的访问范围，也就是什么级别保留注解

| 注解范围 | 注解值 | 说明 |
|---|---|---|
| 源码级注解 | @Retention(RetentionPolicy.SOURCE) | 注解信息只保留到Java源码里
| 编译时注解 | @Retention(RetentionPolicy.CLASS)  | 注解信息会保留到Java源码的.class文件里
| 运行时注解 | @Retention(RetentionPolicy.RUNTME) | Java虚拟机运行时也保留注解，可以通过反射读取注解的信息

未指定类型时，默认时ClASS类型

* @Documented 表示被修饰的注解应该包含在被注解项的文档里
* @Inherited 表示注解可以被子类继承
	* @Inherited 元注解是一个标记注解，@Inherited阐述了某个被标注的类型是被继承的。
	* 如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。 
* @Repeatable 表示注解可以在同一项目上应该多次，这个注解时在Java 8引入的，其他的事在Java 5上引入

**三，常见的标准注解**

1，Override

* java.lang.Override是一个标记类型注解，它被用作标注方法。
* 它说明了被标注的方法重载了父类的方法，起到了断言的作用。
* 如果我们使用了这种注解在一个没有覆盖父类方法的方法时，java编译器将以一个编译错误来警示。

2，Deprecated

* Deprecated也是一种标记类型注解。当一个类型或者类型成员使用@Deprecated修饰的话，编译器将不鼓励使用这个被标注的程序元素。
* 所以使用这种修饰具有一定的“延续性”：如果我们在代码中通过继承或者覆盖的方式使用了这个过时的类型或者成员，
* 虽然继承或者覆盖后的类型或者成员并不是被声明为@Deprecated，但编译器仍然要报警。

3，SuppressWarnings

* SuppressWarning不是一个标记类型注解。
* 它有一个类型为String[]的成员，这个成员的值为被禁止的警告名。
* 对于javac编译器来讲，被-Xlint选项有效的警告名也同样对@SuppressWarings有效，同时编译器忽略掉无法识别的警告名。
* @SuppressWarnings("unchecked")

**四，自定义注解**
	
	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.FIELD)
	public @interface Friut {
		enum Color { BLUE, RED, GREEN};
		/**
		 * 水果颜色
		 * @return
		 */
		Color fruitColor() default Color.RED;
	}

**五，运行时注解**

* 自定义运行时注解（编译时注解和运行时注解有点像Android虚拟机art和Dalvik的区别，预加载和运行时加载）
* 一般和反射配合使用，相比于编译时注解性能比较低，但灵活性好，实现起来比较简单

**六，编译时注解**

能够自动处理Java源文件，并且能够生成更多源码，配置文件，脚本或者其他可能想生成的东西
Java编译器集成了注解处理，通过在编译期间调用javac -processor命令调起注解处理器，它能够允许我们实现编译时注解功能，从而提高函数库的性能

1，定义注解处理器
注解处理器只能产生新文件，不能修改已经有的文件

* 继承AbstractProcessor 并实现progress方法，同时要指定这个注解处理器能够处理的注解类型以及支持的Java版本

2，注册注解处理器

* 定义好注解处理器之后，为了让javac -processor能对其进行处理，需要对其进行注册
* 将注解处理器打包到一个Jar包中，同时在jar包中新增一个名为javax.annotation.processing,Processor的文件来指明jar文件中有哪些注解处理器
* 这个文件最终的路径位于jar文件根目录中的META_INF/services下
* 文件的内容是注解处理器的全路径，如果存在多个，以换行进行分割
* 语句如下：com.annotation.processor.AnnotationProcessor

手动执行上面的注册流程是很繁琐的，Google开源的Autoservices库，引入这个库之后，我们只需要@AutoService(Processor.class)就可以了

**七，Android的apt插件**

我们知道注解处理器只是在编译期间有效，到应用运行时不会用到，因此在gradle中引入依赖应该时provided而不是compile，语句如下：

* provided “”

当然我们也可以使用Android-apt插件的方式，它时一个Android studio引入注解处理器的一个辅助插件

只在编译期间引入注解处理器所在的的函数库作为依赖，不会大包到最终生成的Apk中
